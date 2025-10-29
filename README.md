# Flow for Home Assistant

<div align="center">

![Home Assistant](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?style=for-the-badge&logo=home-assistant&logoColor=white)
![Version](https://img.shields.io/badge/version-2.0.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)

**Transform your productivity into measurable goals.**

A streamlined productivity tracking system for Home Assistant that turns time into achievements.

</div>

---

## 📋 Table of Contents

- [Features](#-features)
- [What is Flow?](#-what-is-flow)
- [Installation](#-installation)
- [Setup Guide](#-setup-guide-docker-focused)
- [Dashboard Configuration](#-dashboard-configuration)
- [Usage](#-usage)
- [Customization](#-customization)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)

---

## ✨ Features

### Core Functionality
- ⏱️ **Smart Time Tracking** - Track your productive time with an intelligent timer system
- 🎯 **Dynamic Daily Goals** - Set and adjust daily goals to match your productivity needs
- 🔥 **Streak System** - Build momentum with consecutive day tracking
- 🏷️ **Category Management** - Organize work by categories (Work, Personal, Coding, Design, Engineering)

### Smart Features
- 🏥 **Sick Mode** - Pause your streak without losing progress when you're unwell
- 📈 **Progress Dashboard** - Real-time visualization of your daily achievement
- 🔔 **Smart Notifications** - Celebrate wins and gentle reminders
- 📱 **Mobile Ready** - Full control from your phone
- 🐳 **Docker Optimized** - 100% configurable via Home Assistant Web UI

---

## 🎯 What is Flow?

Flow is a productivity tracking system designed specifically for Home Assistant Docker installations. It helps you:

1. **Track Time**: Start a timer when you begin productive work
2. **Categorize Work**: Choose from predefined categories (Work, Coding, Design, etc.)
3. **Meet Daily Goals**: Set daily time goals and track your progress
4. **Build Streaks**: Maintain consecutive days of meeting your goals
5. **Stay Motivated**: Get notifications when you reach milestones

### Key Components

**Entities Created:**
- 3 Input Numbers (Daily Goal, Time Tracked, Streak Counter)
- 7 Input Booleans (Timer, Sick Mode, 5 Categories)
- 2 Input DateTimes (Timer Start, Last Reset)
- 2 Input Texts (Active Category, History Data)
- 5 Template Sensors (Progress, Status, Active Work, Timer Elapsed, Time to Goal)

**Automations:**
- Timer Toggle Handler (start/stop tracking)
- Category Selection (single category active at a time)
- Daily Reset (midnight reset with streak logic)
- Goal Reached Notification (celebrate achievements)

**Scripts:**
- Increase/Decrease Goal (adjust daily targets)
- Reset Day (manual reset if needed)
- Add Time Manually (for retroactive tracking)

---

## 📦 Installation

### Prerequisites

- **Home Assistant** running in Docker (tested with 2024.1.0+)
- Access to Home Assistant Web UI
- Access to configuration files via:
  - File Editor (if available as add-on in your setup)
  - Direct volume access (via Docker volume mount)
  - SSH/SFTP access to configuration directory

> **Note for Docker Users:** This guide assumes you have access to your Home Assistant configuration directory. This is typically mapped as a Docker volume (e.g., `/path/on/host:/config`).

---

## 🚀 Setup Guide (Docker Focused)

### Step 1: Access Configuration Files

You need to access your Home Assistant configuration directory. Choose one of these methods:

**Option A: File Editor Add-on (if available)**
1. Install File Editor from the Home Assistant Add-on Store (if not already installed)
2. Navigate to Settings → Add-ons → File Editor
3. Open File Editor

**Option B: Direct File Access**
1. Access your Docker host machine
2. Navigate to your Home Assistant config directory (typically where you mounted the `/config` volume)
   ```bash
   cd /path/to/your/homeassistant/config
   ```

### Step 2: Copy Configuration Files

Copy the three YAML files from this repository to your Home Assistant configuration directory:

1. **flow.yaml** - Main configuration with all entities and sensors
2. **automations.yaml** - Automations for Flow (append to existing file)
3. **scripts.yaml** - Helper scripts for Flow (append to existing file)

#### Important Notes:

- **If you already have automations.yaml and scripts.yaml:**
  - Open the Flow versions from this repository
  - Copy the content and **append** it to your existing files
  - Do NOT replace your existing files completely

- **For flow.yaml:**
  - Create a new file named `flow.yaml` in your config directory
  - Copy the entire content from the repository

### Step 3: Enable Packages Configuration

You need to tell Home Assistant to load the `flow.yaml` file as a package.

1. Open your `configuration.yaml` file
2. Add or modify the following section:

```yaml
homeassistant:
  packages: !include flow.yaml
```

**If you already have a packages section:**
```yaml
homeassistant:
  packages:
    flow: !include flow.yaml
    # your other packages...
```

**Alternative: Using a packages directory (recommended for multiple packages):**
```yaml
homeassistant:
  packages: !include_dir_named packages/
```
Then create a `packages/` directory and place `flow.yaml` inside it.

### Step 4: Restart Home Assistant

After copying the files and updating configuration.yaml:

1. Go to **Settings** → **System** → **Restart**
2. Click **Restart Home Assistant**
3. Wait for the restart to complete (usually 1-2 minutes)

### Step 5: Verify Installation

After restart, verify that Flow entities were created:

1. Go to **Settings** → **Devices & Services** → **Entities**
2. Search for "flow" - you should see:
   - `input_number.flow_daily_goal`
   - `input_number.flow_time_tracked_today`
   - `input_number.flow_streak_counter`
   - `input_boolean.flow_timer_active`
   - `input_boolean.flow_sick_mode`
   - `input_boolean.flow_cat_work`
   - `input_boolean.flow_cat_privat`
   - `input_boolean.flow_cat_coding`
   - `input_boolean.flow_cat_design`
   - `input_boolean.flow_cat_engineering`
   - `input_datetime.flow_timer_start`
   - `input_datetime.flow_last_reset`
   - `input_text.flow_active_category`
   - `input_text.flow_history_data`
   - `sensor.flow_goal_progress`
   - `sensor.flow_status`
   - `sensor.flow_active_work`
   - `sensor.flow_timer_elapsed`
   - `sensor.flow_time_to_goal`

3. Check automations:
   - Go to **Settings** → **Automations & Scenes**
   - You should see 4 new automations starting with "Flow -"

4. Check scripts:
   - Go to **Settings** → **Automations & Scenes** → **Scripts** tab
   - You should see scripts starting with "flow_"

### Step 6: Set Initial Values (via Web UI)

Now configure Flow entirely through the Web UI - no YAML editing needed!

1. **Set Your Daily Goal:**
   - Go to **Settings** → **Devices & Services** → **Entities**
   - Search for `input_number.flow_daily_goal`
   - Click on it and set your desired daily goal in minutes (e.g., 120 for 2 hours)

2. **Initialize Other Values (Optional):**
   - All other entities are automatically initialized with sensible defaults
   - You can adjust them later as needed

---

## 📊 Dashboard Configuration

### Create a Flow Dashboard

1. Go to your Home Assistant dashboard
2. Click the three dots (⋮) in the top right
3. Select **Edit Dashboard**
4. Click **+ Add Card**
5. Select **Manual Card** at the bottom
6. Copy and paste the following YAML:

```yaml
type: vertical-stack
cards:
  - type: tile
    entity: sensor.flow_goal_progress
    name: Fortschritt
    icon: mdi:progress-helper
    color: green
    vertical: false
    features:
      - type: bar-gauge
    features_position: bottom
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_number.flow_time_tracked_today
        name: Getrackt
        color: blue
        vertical: false
        tap_action:
          action: none
        icon_tap_action:
          action: none
      
      - type: tile
        entity: input_number.flow_streak_counter
        name: Streak
        color: orange
        vertical: false
        tap_action:
          action: none
        icon_tap_action:
          action: none
  
  - type: tile
    entity: input_boolean.flow_timer_active
    name: Timer
    color: red
    hide_state: true
    vertical: false
    features:
      - type: toggle
    features_position: inline
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.flow_cat_work
        name: Work
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_privat
        name: Privat
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_coding
        name: Coding
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_design
        name: Design
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_engineering
        name: Engineer
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
  
  - type: history-graph
    entities:
      - entity: sensor.flow_active_work
    hours_to_show: 10
  
  - type: tile
    entity: input_boolean.flow_sick_mode
    name: Krankheitsmodus
    color: blue-grey
    vertical: false
    tap_action:
      action: toggle
```

7. Click **Save**

### Alternative: Simple Card View

For a simpler dashboard card:

```yaml
type: entities
entities:
  - entity: sensor.flow_status
  - entity: input_boolean.flow_timer_active
  - entity: sensor.flow_goal_progress
  - entity: input_number.flow_daily_goal
  - entity: input_number.flow_time_tracked_today
  - entity: input_number.flow_streak_counter
title: Flow Tracker
```

---

## 💡 Usage

### Starting a Work Session

1. **Select a Category** (optional but recommended):
   - Tap one of the category buttons (Work, Privat, Coding, Design, Engineering)
   - Only one category can be active at a time

2. **Start the Timer**:
   - Toggle the Timer switch to ON
   - You'll receive a notification confirming the start

3. **Work on Your Task**:
   - The timer tracks your time automatically
   - You can see elapsed time in real-time

4. **Stop the Timer**:
   - Toggle the Timer switch to OFF
   - Time is automatically added to your daily total
   - You'll receive a notification with session details

### Daily Workflow

1. **Morning**: Check your daily goal and adjust if needed
2. **During the Day**: Track work sessions with the timer
3. **Evening**: Check your progress towards the daily goal
4. **Midnight**: Automatic reset occurs
   - If goal reached: Streak increments
   - If sick mode: Streak preserved
   - Otherwise: Streak resets to 0

### Using Sick Mode

When you're sick or unable to work:

1. Enable **Sick Mode** toggle before midnight
2. Your streak will be preserved during the daily reset
3. Sick mode automatically turns off after midnight

### Adjusting Daily Goal (via Web UI)

**Method 1: Direct Edit**
1. Go to **Settings** → **Devices & Services** → **Entities**
2. Search for `input_number.flow_daily_goal`
3. Click on it and change the value

**Method 2: Using Scripts**
1. Go to **Settings** → **Automations & Scenes** → **Scripts**
2. Run `flow_increase_goal` or `flow_decrease_goal` (each changes by 5 minutes)

**Method 3: From Dashboard**
1. Add a Number card to your dashboard for `input_number.flow_daily_goal`
2. Adjust directly from the dashboard

---

## 🎨 Customization

All customizations can be done through the Home Assistant Web UI!

### Adding New Categories

1. Go to **Settings** → **Devices & Services** → **Helpers**
2. Click **+ Create Helper** → **Toggle**
3. Name it following the pattern: `Flow Cat YourCategory` (entity_id will be `input_boolean.flow_cat_yourcategory`)
4. Choose an icon (e.g., `mdi:your-icon`)
5. Click **Create**

6. Update the automation:
   - Go to **Settings** → **Automations & Scenes**
   - Find "Flow - Kategorie Selection"
   - Click **Edit**
   - Add your new entity to:
     - Triggers list
     - The `category_map` variable
     - The entities list in the service call
   - Click **Save**

### Changing Reset Time

1. Go to **Settings** → **Automations & Scenes**
2. Find "Flow - Daily Reset"
3. Click **Edit**
4. Change the trigger time from `00:00:00` to your preferred time
5. Click **Save**

### Modifying Goal Increment Steps

1. Go to **Settings** → **Automations & Scenes** → **Scripts**
2. Edit `flow_increase_goal` or `flow_decrease_goal`
3. Change the value `+ 5` or `- 5` to your preferred increment
4. Click **Save**

### Customizing Notifications

1. Go to **Settings** → **Automations & Scenes**
2. Find the Flow automations
3. Edit the `persistent_notification.create` service calls
4. Modify the title and message text as desired
5. Click **Save**

---

## 🔧 Troubleshooting

### Entities Not Appearing After Restart

**Solution:**
1. Check that `flow.yaml` is in the correct location
2. Verify `configuration.yaml` has the packages section
3. Check for YAML syntax errors:
   - Go to **Settings** → **System** → **Restart**
   - Click **Check Configuration** before restarting
4. Review Home Assistant logs:
   - Go to **Settings** → **System** → **Logs**

### Timer Not Tracking Time

**Solution:**
1. Verify the timer started:
   - Check `input_datetime.flow_timer_start` has a recent timestamp
2. Check automation is enabled:
   - Go to **Settings** → **Automations & Scenes**
   - Ensure "Flow - Timer Toggle Handler" is enabled
3. Review automation logs:
   - Click on the automation → **Traces** tab

### Automations Not Triggering

**Solution:**
1. Check automations are enabled (not accidentally disabled)
2. Verify entity IDs match (all should use `flow_` prefix)
3. Check automation mode (should be `single`)
4. Review traces for errors

### Daily Reset Not Working

**Solution:**
1. Verify "Flow - Daily Reset" automation is enabled
2. Check the trigger time is correct
3. Ensure Home Assistant Docker container timezone is set correctly:
   ```bash
   docker exec -it homeassistant date
   ```
4. If timezone is wrong, update your Docker compose file:
   ```yaml
   environment:
     - TZ=Europe/Berlin  # Your timezone
   ```

### Streak Not Incrementing

**Solution:**
1. Goal must be reached (100% or more) before midnight
2. Verify `sensor.flow_goal_progress` shows >= 100
3. Check `input_number.flow_time_tracked_today` >= `input_number.flow_daily_goal`
4. Review "Flow - Daily Reset" automation traces

### Configuration Changes Not Taking Effect

**Solution:**
1. After editing YAML files, always restart Home Assistant
2. Check Configuration before restarting
3. Wait for full restart (watch the logs)
4. Clear browser cache if dashboard looks wrong

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Report Bugs**: Open an issue with detailed description
2. **Suggest Features**: Share your ideas for improvements
3. **Submit Pull Requests**: Contribute code improvements
4. **Improve Documentation**: Help make the README clearer

### Development Guidelines

- Maintain Docker compatibility
- Ensure 100% Web UI configurability
- Test changes before submitting
- Update documentation for new features
- Follow existing code style

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Home Assistant Community
- [**@v-berlin**](https://github.com/v-berlin/) for the original concept
- All contributors and users

---

<div align="center">

**Built with ❤️ for the Home Assistant Docker Community**

[Report Bug](https://github.com/v-berlin/Flow/issues) · [Request Feature](https://github.com/v-berlin/Flow/issues)

</div>
