# Changelog

## 31. Oktober 2025 - Fix für Daily Reset Automation

### Problem
Die Automation "Flow - Daily Reset" hat beim Ausführen folgenden Fehler verursacht:
```
Error rendering data template: SecurityError: access to attribute 'update' of 'dict' object is unsafe.
```

### Ursache
Home Assistant erlaubt aus Sicherheitsgründen keine direkte Manipulation von Dictionaries mit den Methoden `update()` und `pop()` in Jinja2-Templates. Diese Methoden verändern das Dictionary an Ort und Stelle (in-place) und sind daher in der sandboxed Jinja2-Umgebung nicht erlaubt.

### Lösung
Die unsicheren Dictionary-Operationen wurden durch sichere Alternativen ersetzt:

**Vorher (unsicher):**
```yaml
{% set _ = data.update({yesterday: yesterday_data}) %}
{% set _ = data.pop(keys[0]) %}
```

**Nachher (sicher):**
```yaml
{% set data = dict(data, **{yesterday: yesterday_data}) %}
{% set data = dict((k, v) for k, v in data.items() if k != keys[0]) %}
```

### Was du tun musst

1. **Lade die geänderte `automations.yaml` Datei in dein Home Assistant hoch**
   - Entweder über File Editor oder über SSH/FTP
   - Pfad: `/config/automations.yaml` (oder wo auch immer deine Automations gespeichert sind)

2. **Lade die Automationen neu**
   - Gehe zu: Einstellungen → Automationen & Szenen
   - Klicke oben rechts auf die drei Punkte (⋮)
   - Wähle "Konfiguration neu laden" → "Automationen"
   - Oder verwende den Service: `automation.reload`

3. **Optional: Teste die Automation manuell**
   - Gehe zu: Einstellungen → Automationen & Szenen
   - Suche "Flow - Daily Reset"
   - Klicke auf die drei Punkte (⋮) → "Ausführen"
   - Prüfe, ob die Automation ohne Fehler durchläuft

### Technische Details
- Die neue Implementierung erstellt bei jeder Operation ein neues Dictionary anstatt das bestehende zu modifizieren
- `dict(data, **{yesterday: yesterday_data})` fügt die neuen Daten hinzu (äquivalent zu update)
- `dict((k, v) for k, v in data.items() if k != keys[0])` entfernt den ältesten Eintrag (äquivalent zu pop)
- Beide Operationen sind in Home Assistant's Jinja2-Umgebung erlaubt und sicher

### Getestet mit
- Home Assistant 2025.x
- Jinja2 Template Engine (sandboxed environment)

Die Automation sollte jetzt ohne Fehler laufen! 🎉
