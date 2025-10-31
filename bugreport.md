# Bug Report: Streak und Daily Reset funktionieren nicht

## Problem Beschreibung
Wenn es 0 Uhr (Mitternacht) ist, passiert nichts. Weder der Streak ändert sich noch setzt sich das Zeug vom Tag zurück.

## Identifizierte Fehler

### **HAUPTFEHLER: Variable `yesterday` wird im Template nicht korrekt verwendet**

**Ort:** `automations.yaml`, Zeile 140

**Das Problem:**

In der Daily Reset Automation (Zeilen 119-205) wird die Variable `yesterday` in Zeile 131 definiert:

```yaml
yesterday: '{{ (now() - timedelta(days=1)).strftime(''%Y-%m-%d'') }}'
```

Diese Variable wird dann in Zeile 140 im History-Update-Template verwendet:

```yaml
value: "{% set history = states('input_text.flow_history_data') %} ... {% set _ = data.update({\n  yesterday: {\n    'percent': progress | round(1),\n    'minutes': minutes,\n    'sick': sick_mode\n  }\n}) %} ..."
```

**DER FEHLER:**

Im Template (Zeile 140) steht `yesterday:` als Schlüssel im Dictionary. Das ist aber ein **String-Literal** und NICHT die Variable `yesterday`!

In Jinja2 Templates innerhalb von YAML-Strings müssen Variablen mit `{{ variable }}` referenziert werden.

Aktuell wird das Dictionary so erstellt:
```python
{
  "yesterday": {  # <- Das ist der LITERAL STRING "yesterday", nicht der Variablen-Wert!
    "percent": 105.5,
    "minutes": 127,
    "sick": false
  }
}
```

Es sollte aber so sein:
```python
{
  "2025-10-30": {  # <- Das sollte das DATUM von gestern sein!
    "percent": 105.5,
    "minutes": 127,
    "sick": false
  }
}
```

**Das bedeutet:**
- Die Historie wird IMMER mit dem Schlüssel "yesterday" (als String) gespeichert
- Jeden Tag wird derselbe Key überschrieben
- Es wird keine echte Historie aufgebaut
- Das erklärt, warum nichts passiert - die History-Logik schlägt fehl!

### **ZWEITER FEHLER: Variablen im Template nicht verfügbar**

**Ort:** `automations.yaml`, Zeile 140

**Das Problem:**

Im `input_text.set_value` Service-Call wird ein komplexes Template als `value` übergeben. Innerhalb dieses Templates werden die Variablen `progress`, `minutes`, und `sick_mode` verwendet:

```yaml
'percent': progress | round(1),
'minutes': minutes,
'sick': sick_mode
```

**ABER:** Diese Variablen wurden zwar in Zeilen 127-133 in einem `variables:` Block definiert, JEDOCH funktioniert der Scope von Variablen in Home Assistant Automations so:

- Variablen die im `variables:` Action-Step definiert werden, sind nur in **nachfolgenden** Action-Steps verfügbar
- Sie sind NICHT automatisch im Template-Scope eines späteren Service-Calls verfügbar
- Man muss sie explizit mit `{{ variable }}` referenzieren

**Aktueller Code:**
```yaml
value: "... 'percent': progress | round(1) ..."
```

**Korrekt wäre:**
```yaml
value: "... 'percent': {{ progress }} | round(1) ..."
```

**ABER MOMENT:** Schauen wir uns die Zeile 140 genauer an. Es ist ein **String-Wert** mit einem Template darin:

```yaml
value: "{% set history = ... %} ... {{ data | to_json }}"
```

Das ist ein vollständiges Jinja2-Template! In diesem Fall SOLLTEN die Variablen eigentlich verfügbar sein, weil Home Assistant die Variablen in den Template-Kontext injiziert.

**ABER:** Das funktioniert NUR wenn die Syntax korrekt ist. Schauen wir uns die Zeile nochmal an:

```yaml
{% set _ = data.update({\n  yesterday: {\n    'percent': progress | round(1), ...
```

Hier sehen wir:
- `yesterday:` ohne `{{ }}` - wird als String-Literal interpretiert ❌
- `progress` ohne `{{ }}` - wird als VARIABLE-NAME interpretiert, nicht als String ✓
- `minutes` ohne `{{ }}` - wird als VARIABLE-NAME interpretiert ✓
- `sick_mode` ohne `{{ }}` - wird als VARIABLE-NAME interpretiert ✓

**ALSO:** Der zweite Fehler ist eigentlich KEIN Fehler für `progress`, `minutes`, und `sick_mode` innerhalb des Templates, ABER es ist ein Fehler für `yesterday`!

## **FINALER FEHLER ERKANNT**

**Ort:** `automations.yaml`, Zeile 140

**Der Fehler ist eindeutig:**

In Zeile 140 muss `yesterday:` zu `{{ yesterday }}:` geändert werden, ABER das ist NICHT valide Jinja2-Syntax für Dictionary-Keys!

Die korrekte Syntax in Jinja2 ist:

```jinja2
{% set _ = data.update({
  {{ yesterday }}: {
    'percent': progress | round(1),
    'minutes': minutes,
    'sick': sick_mode
  }
}) %}
```

**ABER NEIN!** Das funktioniert auch nicht, weil `{{ }}` in Dictionary-Definitionen nicht direkt verwendet werden kann.

**Die KORREKTE Lösung:**

Man muss das Dictionary so erstellen:

```jinja2
{% set yesterday_data = {
  'percent': progress | round(1),
  'minutes': minutes,
  'sick': sick_mode
} %}
{% set _ = data.update({yesterday: yesterday_data}) %}
```

**ODER noch besser:**

Man verwendet die `update()` Methode mit einem String-Index:

```jinja2
{% set _ = data.__setitem__(yesterday, {
  'percent': progress | round(1),
  'minutes': minutes,
  'sick': sick_mode
}) %}
```

## LÖSUNG

### Der Fehler muss in `automations.yaml`, Zeilen 138-144 behoben werden

**Aktueller fehlerhafter Code:**
```yaml
data:
  value: "{% set history = states('input_text.flow_history_data') %} {% if history
    in ['unknown', 'unavailable', '', '{}'] %}\n  {% set history = '{}' %}\n{%
    endif %} {% set data = history | from_json %} {% set _ = data.update({\n  yesterday:
    {\n    'percent': progress | round(1),\n    'minutes': minutes,\n    'sick':
    sick_mode\n  }\n}) %} {% set keys = data.keys() | list | sort %} {% if keys
    | length > 365 %}\n  {% set _ = data.pop(keys[0]) %}\n{% endif %} {{ data
    | to_json }}\n"
```

**Korrigierter Code:**
```yaml
data:
  value: >
    {% set history = states('input_text.flow_history_data') %}
    {% if history in ['unknown', 'unavailable', '', '{}'] %}
      {% set history = '{}' %}
    {% endif %}
    {% set data = history | from_json %}
    {% set yesterday_data = {
      'percent': progress | round(1),
      'minutes': minutes,
      'sick': sick_mode
    } %}
    {% set _ = data.update({yesterday: yesterday_data}) %}
    {% set keys = data.keys() | list | sort %}
    {% if keys | length > 365 %}
      {% set _ = data.pop(keys[0]) %}
    {% endif %}
    {{ data | to_json }}
```

**Erklärung der Änderungen:**

1. **Multi-Line YAML String:** Geändert von `"..."` zu `>` für bessere Lesbarkeit
2. **Zwei-Schritt Dictionary Update:**
   - Erst wird `yesterday_data` Dictionary erstellt
   - Dann wird es mit `{yesterday: yesterday_data}` in `data` eingefügt
   - `yesterday` ist hier die VARIABLE (ohne Quotes), nicht ein String-Literal

3. **Entfernt:** Die `\n` Escape-Sequenzen - nicht nötig mit `>` Syntax

## ZUSAMMENFASSUNG

**Der Hauptfehler:** Die Variable `yesterday` wurde im History-Update-Template als String-Literal verwendet statt als Variable-Referenz. Dies führte dazu, dass:

1. Die Historie immer mit dem Key "yesterday" gespeichert wurde (überschreibt sich jeden Tag)
2. Keine echte Historie aufgebaut wurde
3. Die Daily Reset Logic möglicherweise fehlschlug, weil die History-Daten nicht korrekt waren

**Die Lösung:** Das Template muss so umgeschrieben werden, dass `yesterday` als Variable verwendet wird, indem man ein Zwischenschritt-Dictionary erstellt und dieses dann mit dem korrekten Key in die Historie einfügt.
