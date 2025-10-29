# Flow for Home Assistant

<div align="center">

![Home Assistant](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?style=for-the-badge&logo=home-assistant&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Version](https://img.shields.io/badge/version-2.0.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)

**Transform your productivity into measurable achievements - Built for Home Assistant Docker**

A beautifully simple productivity tracking system that works entirely through Home Assistant's Web UI.

</div>

---

## 📖 What is Flow?

Flow is a minimalistic productivity tracker for Home Assistant that helps you:
- ⏱️ **Track productive time** with a simple start/stop timer
- 🎯 **Set daily goals** and monitor your progress
- 🔥 **Build streaks** by consistently reaching your goals
- 🏷️ **Categorize work** into different types (Work, Personal, Coding, Design, Other)
- 🏥 **Maintain streaks** even during sick days

**Key Philosophy:** Less is more. Flow focuses on essential features without overwhelming complexity.

---

## ✨ Features

### Core Functionality
- **Simple Timer System** - One-click start/stop timer to track your productive sessions
- **Daily Goal Tracking** - Set a daily goal in minutes and track your progress
- **Streak Counter** - Build motivation by maintaining consecutive days of goal achievement
- **Category Management** - Organize your work into 5 customizable categories
- **Sick Mode** - Pause your streak without losing progress when you're unwell

### Built for Docker
- ✅ **100% Web UI Setup** - No YAML file editing required
- ✅ **Home Assistant Docker Compatible** - Works perfectly in containerized environments
- ✅ **No Add-ons Needed** - Uses only built-in Home Assistant features
- ✅ **Mobile Friendly** - Full control from your smartphone

---

## 🎯 How It Works

1. **Select a Category** - Choose what type of work you're doing (Work, Personal, Coding, etc.)
2. **Start the Timer** - Toggle the timer on to begin tracking
3. **Work in Flow** - Focus on your task while Flow tracks your time
4. **Stop the Timer** - Toggle off when done, and your time is automatically added to today's total
5. **Reach Your Goal** - Meet your daily goal to maintain your streak!
6. **Midnight Reset** - At midnight, if you reached your goal, your streak increases; otherwise, it resets

---

## 📦 Prerequisites

### Required
- **Home Assistant** version 2024.1.0 or newer
- **Home Assistant Docker** (or any Home Assistant installation with Web UI access)
- Access to Home Assistant's Web UI

### NOT Required
- ❌ File system access
- ❌ YAML editing capabilities
- ❌ Add-ons
- ❌ Terminal/SSH access
- ❌ Home Assistant Supervisor

---

## 🚀 Complete Setup Guide

This guide will walk you through setting up Flow entirely through the Home Assistant Web UI. No file editing needed!

### Estimated Setup Time: 15-20 minutes

---

## Step 1: Create Helper Entities

Helpers are the foundation of Flow. We'll create all necessary entities through the Web UI.

### 1.1 Create Number Helpers

Navigate to: **Settings** → **Devices & Services** → **Helpers** tab → **+ CREATE HELPER** → **Number**

Create the following **3 Number helpers** with these exact settings:

#### Number 1: Daily Goal
- **Name:** `Flow: Daily Goal`
- **Icon:** `mdi:target`
- **Minimum value:** `0`
- **Maximum value:** `1440`
- **Step size:** `5`
- **Unit of measurement:** `min`
- **Display mode:** Box
- Click **CREATE**

#### Number 2: Time Tracked Today
- **Name:** `Flow: Time Tracked Today`
- **Icon:** `mdi:clock-check`
- **Minimum value:** `0`
- **Maximum value:** `1440`
- **Step size:** `1`
- **Unit of measurement:** `min`
- **Display mode:** Box
- Click **CREATE**

#### Number 3: Streak Days
- **Name:** `Flow: Streak Days`
- **Icon:** `mdi:fire`
- **Minimum value:** `0`
- **Maximum value:** `9999`
- **Step size:** `1`
- **Display mode:** Box
- Click **CREATE**

---

### 1.2 Create Toggle Helpers

Navigate to: **Settings** → **Devices & Services** → **Helpers** tab → **+ CREATE HELPER** → **Toggle**

Create the following **7 Toggle helpers**:

#### Toggle 1: Timer Active
- **Name:** `Flow: Timer Active`
- **Icon:** `mdi:play-pause`
- Click **CREATE**

#### Toggle 2: Sick Mode
- **Name:** `Flow: Sick Mode`
- **Icon:** `mdi:hospital-box`
- Click **CREATE**

#### Toggle 3: Work Category
- **Name:** `Flow: Work`
- **Icon:** `mdi:briefcase`
- Click **CREATE**

#### Toggle 4: Personal Category
- **Name:** `Flow: Personal`
- **Icon:** `mdi:home`
- Click **CREATE**

#### Toggle 5: Coding Category
- **Name:** `Flow: Coding`
- **Icon:** `mdi:code-braces`
- Click **CREATE**

#### Toggle 6: Design Category
- **Name:** `Flow: Design`
- **Icon:** `mdi:palette`
- Click **CREATE**

#### Toggle 7: Other Category
- **Name:** `Flow: Other`
- **Icon:** `mdi:circle-outline`
- Click **CREATE**

---

### 1.3 Create Date/Time Helper

Navigate to: **Settings** → **Devices & Services** → **Helpers** tab → **+ CREATE HELPER** → **Date and/or time**

#### Date/Time: Timer Start Time
- **Name:** `Flow: Timer Start Time`
- **Has date:** ✅ (checked)
- **Has time:** ✅ (checked)
- Click **CREATE**

---

### 1.4 Create Text Helper

Navigate to: **Settings** → **Devices & Services** → **Helpers** tab → **+ CREATE HELPER** → **Text**

#### Text: Active Category
- **Name:** `Flow: Active Category`
- **Maximum length:** `20`
- **Mode:** Text
- **Initial value:** `None`
- Click **CREATE**

---

### 1.5 Create Template Sensors

Navigate to: **Settings** → **Devices & Services** → **Helpers** tab → **+ CREATE HELPER** → **Template** → **Template a sensor**

Create the following **4 Template sensors**:

#### Template Sensor 1: Goal Progress

Click **+ CREATE HELPER** → **Template** → **Template a sensor**

- **Name:** `Flow: Goal Progress`
- **State template:**
```jinja
{% set goal = states('input_number.flow_daily_goal') | float(0) %}
{% set tracked = states('input_number.flow_time_tracked_today') | float(0) %}
{% if goal > 0 %}
  {{ ((tracked / goal) * 100) | round(1) }}
{% else %}
  0
{% endif %}
```
- **Unit of measurement:** `%`
- **Device class:** Leave empty
- **State class:** Leave empty
- **Icon template:**
```jinja
mdi:percent-circle
```
- Click **CREATE**

#### Template Sensor 2: Status

Click **+ CREATE HELPER** → **Template** → **Template a sensor**

- **Name:** `Flow: Status`
- **State template:**
```jinja
{% set category = states('input_text.flow_active_category') %}
{% set progress = states('sensor.flow_goal_progress') | float(0) %}
{% if states('input_boolean.flow_timer_active') == 'on' %}
  🔴 {{ category }} running...
{% elif states('input_boolean.flow_sick_mode') == 'on' %}
  🏥 Sick Mode
{% elif progress >= 100 %}
  ✅ Goal Reached!
{% else %}
  Ready to Start 🚀
{% endif %}
```
- **Icon template:**
```jinja
{% if states('input_boolean.flow_timer_active') == 'on' %}
  mdi:record-circle
{% else %}
  mdi:information-outline
{% endif %}
```
- Click **CREATE**

#### Template Sensor 3: Active Work

Click **+ CREATE HELPER** → **Template** → **Template a sensor**

- **Name:** `Flow: Active Work`
- **State template:**
```jinja
{% if states('input_boolean.flow_timer_active') == 'on' %}
  {{ states('input_text.flow_active_category') }}
{% else %}
  Inactive
{% endif %}
```
- **Icon template:**
```jinja
mdi:briefcase
```
- Click **CREATE**

#### Template Sensor 4: Timer Elapsed

Click **+ CREATE HELPER** → **Template** → **Template a sensor**

- **Name:** `Flow: Timer Elapsed`
- **State template:**
```jinja
{% if states('input_boolean.flow_timer_active') == 'on' %}
  {% set start = states('input_datetime.flow_timer_start') | as_datetime %}
  {% if start %}
    {% set elapsed = (now() - start).total_seconds() / 60 %}
    {{ elapsed | round(0) }}
  {% else %}
    0
  {% endif %}
{% else %}
  0
{% endif %}
```
- **Unit of measurement:** `min`
- **Icon template:**
```jinja
mdi:timer
```
- Click **CREATE**

---

## Step 2: Create Automations

Now we'll create the automations that make Flow work automatically.

Navigate to: **Settings** → **Automations & Scenes** → **Automations** tab → **+ CREATE AUTOMATION** → **Create new automation**

### 2.1 Automation: Timer Toggle Handler

1. Click **+ CREATE AUTOMATION** → **Create new automation**
2. Click the **⋮** (three dots) in the top right → **Edit in YAML**
3. Paste this YAML:

```yaml
alias: "Flow: Timer Toggle Handler"
description: "Starts or stops the productivity timer"
trigger:
  - platform: state
    entity_id: input_boolean.flow_timer_active
    from: "off"
    to: "on"
    id: timer_start
  - platform: state
    entity_id: input_boolean.flow_timer_active
    from: "on"
    to: "off"
    id: timer_stop
condition: []
action:
  - choose:
      - conditions:
          - condition: trigger
            id: timer_start
        sequence:
          - service: input_datetime.set_datetime
            target:
              entity_id: input_datetime.flow_timer_start
            data:
              datetime: "{{ now() }}"
          - service: persistent_notification.create
            data:
              title: "⏱️ Timer Started"
              message: "Category: {{ states('input_text.flow_active_category') }}"
      - conditions:
          - condition: trigger
            id: timer_stop
        sequence:
          - variables:
              start_time: "{{ state_attr('input_datetime.flow_timer_start', 'timestamp') }}"
              elapsed_minutes: "{{ ((as_timestamp(now()) - start_time) / 60) | int }}"
              current_total: "{{ states('input_number.flow_time_tracked_today') | int }}"
              new_total: "{{ [current_total + elapsed_minutes, 1440] | min }}"
          - service: input_number.set_value
            target:
              entity_id: input_number.flow_time_tracked_today
            data:
              value: "{{ new_total }}"
          - service: persistent_notification.create
            data:
              title: "⏹️ Timer Stopped"
              message: "{{ elapsed_minutes }} minutes tracked. Total today: {{ new_total }} min"
mode: single
```

4. Click **SAVE**

---

### 2.2 Automation: Category Selection

1. Click **+ CREATE AUTOMATION** → **Create new automation**
2. Click the **⋮** (three dots) → **Edit in YAML**
3. Paste this YAML:

```yaml
alias: "Flow: Category Selection"
description: "Ensures only one category can be active at a time"
trigger:
  - platform: state
    entity_id:
      - input_boolean.flow_cat_work
      - input_boolean.flow_cat_personal
      - input_boolean.flow_cat_coding
      - input_boolean.flow_cat_design
      - input_boolean.flow_cat_other
    to: "on"
condition: []
action:
  - variables:
      triggered_entity: "{{ trigger.entity_id }}"
      category_map:
        input_boolean.flow_cat_work: Work
        input_boolean.flow_cat_personal: Personal
        input_boolean.flow_cat_coding: Coding
        input_boolean.flow_cat_design: Design
        input_boolean.flow_cat_other: Other
      selected_category: "{{ category_map[triggered_entity] }}"
  # First, turn off all other categories
  - service: input_boolean.turn_off
    target:
      entity_id: >
        {% set entities = [
          'input_boolean.flow_cat_work',
          'input_boolean.flow_cat_personal',
          'input_boolean.flow_cat_coding',
          'input_boolean.flow_cat_design',
          'input_boolean.flow_cat_other'
        ] %}
        {{ entities | reject('eq', triggered_entity) | list }}
  # Then ensure the selected category is ON (in case it was toggled off)
  - service: input_boolean.turn_on
    target:
      entity_id: "{{ triggered_entity }}"
  # Finally, update the active category text
  - service: input_text.set_value
    target:
      entity_id: input_text.flow_active_category
    data:
      value: "{{ selected_category }}"
mode: queued
max: 5
```

4. Click **SAVE**

---

### 2.3 Automation: Daily Reset

1. Click **+ CREATE AUTOMATION** → **Create new automation**
2. Click the **⋮** (three dots) → **Edit in YAML**
3. Paste this YAML:

```yaml
alias: "Flow: Daily Reset"
description: "Resets daily counters at midnight and manages streak"
trigger:
  - platform: time
    at: "00:00:00"
condition: []
action:
  - variables:
      goal_reached: "{{ states('sensor.flow_goal_progress') | float(0) >= 100 }}"
      sick_mode: "{{ states('input_boolean.flow_sick_mode') == 'on' }}"
  - choose:
      - conditions:
          - condition: template
            value_template: "{{ sick_mode }}"
        sequence:
          - service: persistent_notification.create
            data:
              title: "🏥 Sick Day"
              message: "Streak maintained at {{ states('input_number.flow_streak_counter') }} days"
      - conditions:
          - condition: template
            value_template: "{{ goal_reached }}"
        sequence:
          - service: input_number.increment
            target:
              entity_id: input_number.flow_streak_counter
          - service: persistent_notification.create
            data:
              title: "🔥 Streak!"
              message: "{{ states('input_number.flow_streak_counter') }} days in a row!"
    default:
      - service: input_number.set_value
        target:
          entity_id: input_number.flow_streak_counter
        data:
          value: 0
      - service: persistent_notification.create
        data:
          title: "💔 Streak Lost"
          message: "Starting fresh today!"
  - service: input_number.set_value
    target:
      entity_id: input_number.flow_time_tracked_today
    data:
      value: 0
  - service: input_boolean.turn_off
    target:
      entity_id:
        - input_boolean.flow_sick_mode
        - input_boolean.flow_timer_active
        - input_boolean.flow_cat_work
        - input_boolean.flow_cat_personal
        - input_boolean.flow_cat_coding
        - input_boolean.flow_cat_design
        - input_boolean.flow_cat_other
  - service: input_text.set_value
    target:
      entity_id: input_text.flow_active_category
    data:
      value: "None"
mode: single
```

4. Click **SAVE**

---

### 2.4 Automation: Goal Reached Notification

1. Click **+ CREATE AUTOMATION** → **Create new automation**
2. Click the **⋮** (three dots) → **Edit in YAML**
3. Paste this YAML:

```yaml
alias: "Flow: Goal Reached Notification"
description: "Notifies when daily goal is reached"
trigger:
  - platform: numeric_state
    entity_id: sensor.flow_goal_progress
    above: 99.9
condition:
  - condition: state
    entity_id: input_boolean.flow_sick_mode
    state: "off"
  - condition: template
    value_template: >
      {{ (now() - this.attributes.last_triggered | default(as_datetime(0))) > timedelta(hours=20) }}
action:
  - service: persistent_notification.create
    data:
      title: "🎉 Daily Goal Reached!"
      message: >
        Congratulations! You reached your goal!
        Tracked: {{ states('input_number.flow_time_tracked_today') }} minutes
        Streak: {{ states('input_number.flow_streak_counter') }} days
mode: single
```

4. Click **SAVE**

---

## Step 3: Create Dashboard

Now let's create a beautiful dashboard to control Flow!

Navigate to: **Settings** → **Dashboards** → **+ ADD DASHBOARD**

1. **Dashboard name:** `Flow Productivity`
2. Click **CREATE**
3. Click on your new dashboard
4. Click **✏️ Edit** (pencil icon top-right)
5. Click **+ ADD CARD**
6. Choose **Manual** at the bottom
7. Paste this YAML configuration:

```yaml
type: vertical-stack
cards:
  - type: tile
    entity: sensor.flow_goal_progress
    name: Daily Progress
    icon: mdi:progress-check
    color: green
    vertical: false
    features:
      - type: target-temperature
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_number.flow_time_tracked_today
        name: Tracked Today
        color: blue
        icon: mdi:clock-check
        tap_action:
          action: none
      - type: tile
        entity: input_number.flow_streak_counter
        name: Streak
        color: orange
        icon: mdi:fire
        tap_action:
          action: none
  - type: tile
    entity: input_boolean.flow_timer_active
    name: Timer
    color: red
    icon: mdi:timer
    features:
      - type: toggle
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.flow_cat_work
        name: Work
        icon: mdi:briefcase
        color: blue
        tap_action:
          action: toggle
      - type: tile
        entity: input_boolean.flow_cat_personal
        name: Personal
        icon: mdi:home
        color: green
        tap_action:
          action: toggle
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.flow_cat_coding
        name: Coding
        icon: mdi:code-braces
        color: purple
        tap_action:
          action: toggle
      - type: tile
        entity: input_boolean.flow_cat_design
        name: Design
        icon: mdi:palette
        color: pink
        tap_action:
          action: toggle
      - type: tile
        entity: input_boolean.flow_cat_other
        name: Other
        icon: mdi:circle-outline
        color: grey
        tap_action:
          action: toggle
  - type: tile
    entity: sensor.flow_status
    name: Status
    icon: mdi:information
  - type: entities
    entities:
      - entity: input_number.flow_daily_goal
        name: Daily Goal (minutes)
      - entity: input_boolean.flow_sick_mode
        name: Sick Mode
    title: Settings
```

8. Click **SAVE**
9. Exit edit mode

---

## 🎮 How to Use Flow

### Setting Your Daily Goal

1. Find the "Settings" card on your dashboard
2. Adjust the **Daily Goal (minutes)** slider to your desired goal (e.g., 240 for 4 hours)

### Starting a Work Session

1. **Select a category** by tapping one of the category buttons (Work, Personal, Coding, Design, Other)
2. **Start the timer** by toggling the Timer switch to ON
3. Work on your task!
4. **Stop the timer** when you're done by toggling it OFF
5. Your time is automatically added to "Tracked Today"

### Using Sick Mode

If you're sick and can't work:
1. Toggle **Sick Mode** to ON in the Settings card
2. Your streak will be preserved at midnight even if you don't reach your goal
3. Remember to turn it off when you're better!

### Monitoring Progress

- **Daily Progress** bar shows your percentage toward the goal
- **Tracked Today** shows how many minutes you've logged
- **Streak** shows how many consecutive days you've reached your goal
- **Status** shows what you're currently doing

---

## 📊 Entity Reference

Here's a complete list of all entities Flow uses:

### Input Numbers (Helpers → Number)
| Entity ID | Name | Purpose |
|-----------|------|---------|
| `input_number.flow_daily_goal` | Flow: Daily Goal | Your daily goal in minutes |
| `input_number.flow_time_tracked_today` | Flow: Time Tracked Today | Today's total tracked time |
| `input_number.flow_streak_counter` | Flow: Streak Days | Current streak of consecutive goal-achieving days |

### Input Booleans (Helpers → Toggle)
| Entity ID | Name | Purpose |
|-----------|------|---------|
| `input_boolean.flow_timer_active` | Flow: Timer Active | Controls the timer (on/off) |
| `input_boolean.flow_sick_mode` | Flow: Sick Mode | Preserves streak on sick days |
| `input_boolean.flow_cat_work` | Flow: Work | Work category selector |
| `input_boolean.flow_cat_personal` | Flow: Personal | Personal category selector |
| `input_boolean.flow_cat_coding` | Flow: Coding | Coding category selector |
| `input_boolean.flow_cat_design` | Flow: Design | Design category selector |
| `input_boolean.flow_cat_other` | Flow: Other | Other category selector |

### Input DateTime (Helpers → Date/Time)
| Entity ID | Name | Purpose |
|-----------|------|---------|
| `input_datetime.flow_timer_start` | Flow: Timer Start Time | Stores when the timer was started |

### Input Text (Helpers → Text)
| Entity ID | Name | Purpose |
|-----------|------|---------|
| `input_text.flow_active_category` | Flow: Active Category | Stores the currently selected category |

### Template Sensors (Helpers → Template)
| Entity ID | Name | Purpose |
|-----------|------|---------|
| `sensor.flow_goal_progress` | Flow: Goal Progress | Shows progress as a percentage |
| `sensor.flow_status` | Flow: Status | Shows current status message |
| `sensor.flow_active_work` | Flow: Active Work | Shows active category while timer runs |
| `sensor.flow_timer_elapsed` | Flow: Timer Elapsed | Shows elapsed time for current session |

### Automations
| Automation Name | Purpose |
|-----------------|---------|
| Flow: Timer Toggle Handler | Manages timer start/stop logic and time tracking |
| Flow: Category Selection | Ensures only one category is active at a time |
| Flow: Daily Reset | Resets counters at midnight and manages streak |
| Flow: Goal Reached Notification | Celebrates when you reach your daily goal |

---

## 🎨 Customization

### Changing Category Names

1. Go to **Settings** → **Devices & Services** → **Helpers**
2. Find the category toggle you want to rename (e.g., `Flow: Other`)
3. Click on it
4. Click the settings (gear) icon
5. Change the **Name** field
6. Click **UPDATE**

### Adding More Categories

To add a new category:

1. **Create a new Toggle helper:**
   - Navigate to **Settings** → **Devices & Services** → **Helpers** → **+ CREATE HELPER** → **Toggle**
   - Name it `Flow: YourCategory` (e.g., `Flow: Gaming`)
   - Choose an appropriate icon

2. **Update the Category Selection automation:**
   - Go to **Settings** → **Automations & Scenes**
   - Find "Flow: Category Selection"
   - Edit it and add your new toggle to:
     - The trigger `entity_id` list
     - The `category_map` in variables
     - The list in `input_boolean.turn_off`

3. **Update the Daily Reset automation:**
   - Find "Flow: Daily Reset"
   - Add your new toggle to the `input_boolean.turn_off` list

4. **Add it to your dashboard:**
   - Edit your dashboard
   - Add a new tile card for your category

### Changing the Reset Time

By default, Flow resets at midnight (00:00:00). To change this:

1. Go to **Settings** → **Automations & Scenes**
2. Find "Flow: Daily Reset"
3. Click **EDIT**
4. Click on the Time trigger
5. Change the time (e.g., to `06:00:00` for 6 AM)
6. Click **SAVE**

### Adjusting Goal Increments

The daily goal can be adjusted in 5-minute increments. To change this:

1. Go to **Settings** → **Devices & Services** → **Helpers**
2. Find `Flow: Daily Goal`
3. Click on it and then the settings (gear) icon
4. Change the **Step size** (e.g., to `10` for 10-minute increments)
5. Click **UPDATE**

---

## 🐛 Troubleshooting

### Timer isn't tracking time correctly

**Problem:** Timer stops but time isn't added to today's total.

**Solution:**
1. Check that the automation "Flow: Timer Toggle Handler" is enabled
2. Go to **Settings** → **Automations & Scenes** → Find the automation
3. Make sure it's enabled (toggle should be ON)
4. Check the automation trace to see if it ran successfully

### Categories aren't working

**Problem:** Multiple categories can be selected at once, or category doesn't change.

**Solution:**
1. Verify the "Flow: Category Selection" automation is enabled
2. Check that all category entity IDs match exactly in the automation
3. Entity names are case-sensitive - make sure they match exactly

### Streak isn't updating

**Problem:** Reached goal but streak didn't increase at midnight.

**Solution:**
1. Check that "Flow: Daily Reset" automation is enabled
2. Verify your time zone is set correctly in Home Assistant
3. Check the automation trace at midnight to see if it ran

### Can't see notifications

**Problem:** Not receiving notifications when timer starts/stops or goal is reached.

**Solution:**
1. Check that you have the Home Assistant mobile app installed
2. Enable notifications in the Home Assistant app
3. Go to your user profile in HA and make sure notifications are enabled

### Template sensors showing "Unknown" or "Unavailable"

**Problem:** Template sensors aren't calculating correctly.

**Solution:**
1. Wait a few minutes - template sensors need time to initialize
2. Restart Home Assistant: **Settings** → **System** → **RESTART**
3. Check that all input helpers are created correctly
4. Verify entity IDs match exactly in template code

---

## 🔄 Backing Up Your Configuration

Since Flow is entirely UI-based, your configuration is stored in Home Assistant's database. To back up:

1. **Use Home Assistant's built-in backup:**
   - Go to **Settings** → **System** → **Backups**
   - Click **CREATE BACKUP**
   - Download the backup file to a safe location

2. **Export individual automations:**
   - Go to **Settings** → **Automations & Scenes**
   - Click on an automation
   - Click **⋮** (three dots) → **Edit in YAML**
   - Copy the YAML and save it to a text file

---

## 🚀 Advanced: Exporting to YAML Files (Optional)

If you want to migrate from UI-based configuration to YAML files (for version control, etc.):

1. **Export Helpers:**
   - Helpers are stored in `.storage/core.config_entries` (not easily editable)
   - Best practice: Keep helpers in UI, use packages for custom sensors

2. **Export Automations:**
   - Automations are already in `automations.yaml` if you enabled YAML mode
   - Otherwise, copy from UI as described in the backup section

3. **Convert to Package:**
   - Use the reference files provided (`flow_reference.yaml`, `flow_automations_reference.yaml`)
   - Create a `packages` directory in your Home Assistant config
   - Move content there

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Report Bugs:** Open an issue describing the problem
2. **Suggest Features:** Open an issue with your feature idea
3. **Submit PRs:** Fork the repo and submit a pull request
4. **Improve Documentation:** Help make the setup guide even clearer

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Home Assistant Community** for the amazing platform
- **[@valentin-berlin](https://github.com/valentin-berlin/)** for the original Motive idea
- **Anthropic Claude** for assistance in refactoring and documentation

---

## 📈 Changelog

### Version 2.0.0 (Flow)
- 🎯 Complete refactoring from Motive to Flow
- ✨ Simplified entity structure (reduced from 20+ to 11 core entities)
- 📝 100% Web UI setup - no YAML editing required
- 🐳 Full Docker compatibility
- 📚 Comprehensive documentation with step-by-step guide
- 🔄 Improved automation logic
- 🎨 Renamed all entities from "motive" to "flow"
- 🧹 Removed unnecessary complexity (history stats, unused sensors)
- 🏥 Simplified sick mode implementation
- 🔥 More reliable streak counter

### Version 1.0.0 (Motive)
- Initial release

---

<div align="center">

**Built with ❤️ for the Home Assistant Community**

**Made for Docker • No YAML Required • 100% Web UI**

</div>
