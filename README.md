# Flow for Home Assistant

<div align="center">

![Home Assistant](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?style=for-the-badge&logo=home-assistant&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Version](https://img.shields.io/badge/version-2.0.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)

**Transform your productivity into measurable achievements.**

A streamlined productivity tracking system for Home Assistant that turns time into tangible progress.

</div>

---

## 📖 Table of Contents

- [About Flow](#about-flow)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Dashboard Setup](#dashboard-setup)
- [Entities Reference](#entities-reference)
- [Customization](#customization)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## 📝 About Flow

**Flow** is a productivity tracking system designed specifically for Home Assistant, with a focus on Docker deployments. It helps you:

- Track time spent on different work categories
- Set and achieve daily goals
- Build and maintain productive streaks
- Visualize your progress in real-time
- Stay motivated with smart notifications

The system is built entirely using Home Assistant's native functionality - no additional integrations or custom components required. Everything can be configured through the Home Assistant Web UI.

### What's Included

- **3 Input Numbers**: Daily goal, tracked time, and streak counter
- **8 Input Booleans**: Timer control, sick mode, and 5 work categories
- **1 Input DateTime**: Timer start tracking
- **1 Input Text**: Active category storage
- **5 Template Sensors**: Real-time progress, status, and time calculations
- **4 Automations**: Timer control, category management, daily reset, and notifications
- **4 Scripts**: Goal adjustment and manual time management

### Design Philosophy

Flow follows these principles:
- **Minimal**: Only essential entities, no bloat
- **Efficient**: Optimized templates and automations
- **Accessible**: 100% configurable via Web UI
- **Docker-First**: Designed for containerized Home Assistant deployments
- **Self-Contained**: No external dependencies

---

## ✨ Features

### Core Functionality
- ⏱️ **Smart Time Tracking** - Track productive time with a simple toggle
- 🎯 **Daily Goals** - Set customizable daily time goals
- 🔥 **Streak System** - Build momentum with consecutive day tracking
- 🏷️ **Category Management** - Organize work (Work, Personal, Coding, Design, Engineering)

### Smart Features
- 🏥 **Sick Mode** - Pause streaks without losing progress
- 📈 **Real-Time Progress** - Live visualization of daily achievement
- 🔔 **Smart Notifications** - Celebrate achievements
- 📱 **Mobile Ready** - Full control from any device
- 🐳 **Docker Optimized** - Perfect for containerized deployments

---

## 📋 Prerequisites

### Required

- **Home Assistant** - Running in Docker container
  - Version 2024.1.0 or newer recommended
  - Docker Compose or standalone Docker container
- **Web Browser** - Access to Home Assistant Web UI
- **Basic Knowledge** - Familiarity with Home Assistant's interface

### Optional

- **Mobile App** - For notifications and mobile access
- **Dashboard Access** - To create custom views

---

## 🚀 Installation

All installation steps are performed through the **Home Assistant Web UI**. No terminal access or SSH required.

### Step 1: Access Your Home Assistant

1. Open your web browser
2. Navigate to your Home Assistant URL (e.g., `http://homeassistant.local:8123` or your custom domain)
3. Log in with your credentials

### Step 2: Enable File Editor Add-on

The File Editor allows you to edit configuration files through the Web UI.

1. Click on **Settings** in the sidebar
2. Click on **Add-ons**
3. Click the **Add-on Store** button (bottom right)
4. Search for "**File editor**"
5. Click on it and press **Install**
6. After installation, click **Start**
7. Enable **Show in sidebar** option
8. The File Editor icon will appear in your sidebar

### Step 3: Enable Packages

Packages allow you to organize your configuration into separate files.

1. Click **File Editor** in the sidebar
2. Click on the folder icon (top left) to browse files
3. Open `configuration.yaml`
4. Find the `homeassistant:` section (usually at the top)
5. Add the following line under `homeassistant:`:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
6. If `homeassistant:` doesn't exist, add the complete block at the top of the file
7. Click the **Save** icon (disk icon, top right)

### Step 4: Create Packages Directory

1. In File Editor, click the **folder with plus** icon (next to the folder icon)
2. Type: `packages`
3. Press Enter or click OK
4. You should now see a `packages` folder in your file browser

### Step 5: Create Flow Package File

1. Click on the `packages` folder to open it
2. Click the **file with plus** icon to create a new file
3. Name it: `flow.yaml`
4. Copy the entire contents from the `flow.yaml` file in this repository
5. Paste it into the new file
6. Click **Save**

### Step 6: Add Automations

1. In File Editor, go back to the root directory (click the folder icon and select root)
2. Open `automations.yaml` (it may be empty or have existing automations)
3. Copy the contents from the `automations_new.yaml` file in this repository
4. Paste at the end of your existing `automations.yaml` file (or replace all if it's empty)
   - **Important**: If you have existing automations, paste the new ones at the end
   - Each automation must have a unique ID (already provided)
5. Click **Save**

### Step 7: Add Scripts

1. In File Editor, open `scripts.yaml` (it may be empty or have existing scripts)
2. Copy the contents from the `scripts_new.yaml` file in this repository
3. Paste at the end of your existing `scripts.yaml` file (or replace all if it's empty)
   - If you have existing scripts, paste the new ones at the end
4. Click **Save**

### Step 8: Check Configuration

1. Click on **Settings** in the sidebar
2. Click on **System**
3. Click on **Restart** (top right)
4. Click **Check Configuration** button
5. Wait for the check to complete
6. If there are errors, review them and fix any typos in your YAML files
7. If configuration is valid, you'll see a green checkmark

### Step 9: Restart Home Assistant

1. If configuration check passed, click the **Restart Home Assistant** button
2. Confirm the restart
3. Wait for Home Assistant to reload (usually 1-2 minutes)
4. You may need to refresh your browser after restart

### Step 10: Verify Installation

1. Click on **Settings** in the sidebar
2. Click on **Devices & Services**
3. Click on **Entities** tab
4. Search for "flow" in the filter box
5. You should see all Flow entities:
   - `input_number.flow_daily_goal`
   - `input_number.flow_time_tracked_today`
   - `input_number.flow_streak_counter`
   - `input_boolean.flow_timer_active`
   - `sensor.flow_goal_progress`
   - And more...

**Congratulations!** Flow is now installed and ready to use.

---

## ⚙️ Configuration

### Initial Setup

After installation, you need to set your daily goal:

1. Click on **Settings** → **Devices & Services** → **Entities**
2. Search for `flow_daily_goal`
3. Click on the entity
4. Set your desired daily goal in minutes (e.g., 240 for 4 hours)
5. Click **Update**

Alternatively, use the **Developer Tools**:

1. Click on **Developer Tools** in the sidebar (bottom of sidebar)
2. Click on **Services** tab
3. Search for service: `input_number.set_value`
4. Select entity: `input_number.flow_daily_goal`
5. Enter value: `240` (or your desired minutes)
6. Click **Call Service**

### Automation IDs

The automations included have the following IDs (do not modify these):
- `flow_timer_toggle_handler` - Manages timer start/stop
- `flow_category_selection` - Handles category switching
- `flow_daily_reset` - Resets counters at midnight
- `flow_goal_reached_notification` - Sends goal completion notification

To view or edit automations:
1. Click on **Settings** → **Automations & Scenes**
2. Search for "Flow"
3. Click on any automation to view or edit

---

## 💡 Usage

### Starting a Work Session

1. **Select a Category**:
   - Navigate to your Flow dashboard (see Dashboard Setup below)
   - Toggle one of the category buttons (Work, Personal, Coding, Design, Engineering)
   - Only one category can be active at a time

2. **Start the Timer**:
   - Toggle the **Timer** button to ON
   - You'll see a notification confirming the timer started
   - The active category will be displayed

3. **Work on Your Task**:
   - The timer runs in the background
   - You can see elapsed time in real-time
   - Progress bar updates automatically

4. **Stop the Timer**:
   - Toggle the **Timer** button to OFF
   - You'll see a notification showing time tracked
   - Time is automatically added to your daily total

### Using Sick Mode

When you're unwell and can't work:

1. Toggle **Sick Mode** ON before midnight
2. Your streak will be preserved during the daily reset
3. The counter resets to 0 but your streak remains
4. Turn OFF Sick Mode when you're ready to work again

### Manual Time Adjustment

To manually add time (e.g., if you forgot to start the timer):

1. Click on **Developer Tools** in the sidebar
2. Click on **Services** tab
3. Search for: `script.flow_add_time`
4. Enter minutes in the `minutes` field
5. Click **Call Service**

---

## 🎨 Dashboard Setup

### Creating Your Flow Dashboard

1. Click on **Overview** in the sidebar (your main dashboard)
2. Click the **three dots** (top right) → **Edit Dashboard**
3. Click **+ Add Card** (bottom right)
4. Choose **Manual** at the bottom
5. Copy and paste this YAML configuration:

```yaml
type: vertical-stack
cards:
  - type: tile
    entity: sensor.flow_goal_progress
    name: Progress
    icon: mdi:progress-helper
    color: green
    features:
      - type: bar-gauge
    features_position: bottom
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_number.flow_time_tracked_today
        name: Tracked
        color: blue
        tap_action:
          action: none
      
      - type: tile
        entity: input_number.flow_streak_counter
        name: Streak
        color: orange
        tap_action:
          action: none
  
  - type: tile
    entity: input_boolean.flow_timer_active
    name: Timer
    color: red
    hide_state: true
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
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_personal
        name: Personal
        color: green
        hide_state: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_coding
        name: Coding
        color: green
        hide_state: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_design
        name: Design
        color: green
        hide_state: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.flow_cat_engineering
        name: Engineer
        color: green
        hide_state: true
        tap_action:
          action: toggle
  
  - type: tile
    entity: input_boolean.flow_sick_mode
    name: Sick Mode
    color: blue-grey
    tap_action:
      action: toggle
```

6. Click **Save** (bottom right)
7. Click **Done** (top right)

Your Flow dashboard is now ready!

---

## 📊 Entities Reference

### Input Numbers

| Entity | Description | Range | Unit |
|--------|-------------|-------|------|
| `input_number.flow_daily_goal` | Daily time goal | 0-1440 | minutes |
| `input_number.flow_time_tracked_today` | Time tracked today | 0-1440 | minutes |
| `input_number.flow_streak_counter` | Current streak | 0-9999 | days |

### Input Booleans

| Entity | Description |
|--------|-------------|
| `input_boolean.flow_timer_active` | Timer on/off state |
| `input_boolean.flow_sick_mode` | Sick mode toggle |
| `input_boolean.flow_cat_work` | Work category |
| `input_boolean.flow_cat_personal` | Personal category |
| `input_boolean.flow_cat_coding` | Coding category |
| `input_boolean.flow_cat_design` | Design category |
| `input_boolean.flow_cat_engineering` | Engineering category |

### Sensors

| Entity | Description | Type |
|--------|-------------|------|
| `sensor.flow_goal_progress` | Progress percentage | Template |
| `sensor.flow_status` | Current status with emoji | Template |
| `sensor.flow_active_work` | Active category when timer runs | Template |
| `sensor.flow_timer_elapsed` | Elapsed time in current session | Template |
| `sensor.flow_time_to_goal` | Remaining time to goal | Template |

### Scripts

| Script | Description |
|--------|-------------|
| `script.flow_increase_goal` | Increase daily goal by 5 minutes |
| `script.flow_decrease_goal` | Decrease daily goal by 5 minutes |
| `script.flow_reset_day` | Manually reset the day |
| `script.flow_add_time` | Add time manually (requires minutes parameter) |

---

## 🎨 Customization

All customizations can be done through the Web UI.

### Adjust Goal Increment Step

To change how much the goal increases/decreases:

1. Go to **Settings** → **Automations & Scenes** → **Scripts**
2. Click on `Flow - Increase Goal` or `Flow - Decrease Goal`
3. Click **Edit**
4. Find the line with `+ 5` or `- 5`
5. Change to your desired value (e.g., `+ 10` for 10-minute increments)
6. Click **Save**

### Add New Category

1. **Add the Input Boolean**:
   - Click **File Editor** → `packages/flow.yaml`
   - Under `input_boolean:` section, add:
   ```yaml
   flow_cat_yourcategory:
     name: "Your Category"
     icon: mdi:your-icon
   ```

2. **Update the Automation**:
   - Open `automations.yaml`
   - Find the `Flow - Category Selection` automation
   - Add your entity to the trigger list and category_map

3. **Save and Restart** Home Assistant

### Change Daily Reset Time

1. Go to **Settings** → **Automations & Scenes**
2. Click on `Flow - Daily Reset`
3. Click **Edit**
4. Click on the time trigger
5. Change to your desired time (e.g., 06:00:00 for 6 AM)
6. Click **Save**

### Customize Notification Messages

1. Go to **Settings** → **Automations & Scenes**
2. Click on the automation you want to customize
3. Click **Edit**
4. Find the `persistent_notification.create` service call
5. Modify the `title` or `message` fields
6. Click **Save**

---

## 🐛 Troubleshooting

### Entities Not Appearing

1. Check configuration:
   - **Settings** → **System** → **Check Configuration**
2. Review Home Assistant logs:
   - **Settings** → **System** → **Logs**
3. Verify file syntax in File Editor
4. Restart Home Assistant after fixes

### Timer Not Tracking Time

1. Verify timer is actually ON (check entity state)
2. Check that a category is selected
3. Review automation logs:
   - **Settings** → **Automations & Scenes**
   - Click on `Flow - Timer Toggle Handler`
   - Click **Traces** to see recent executions

### Automations Not Triggering

1. Check automation is enabled:
   - **Settings** → **Automations & Scenes**
   - Ensure toggle is ON
2. Check condition logic in automation
3. Review traces for failed conditions

### Streak Not Incrementing

1. Verify goal was reached before midnight:
   - Check `sensor.flow_goal_progress` is ≥ 100%
2. Check Sick Mode is OFF
3. Review `Flow - Daily Reset` automation traces

### Lost All Data After Restart

Flow entities are stored in Home Assistant's state machine and may reset after restart. To preserve data:

1. Add to `configuration.yaml`:
   ```yaml
   recorder:
     include:
       entities:
         - input_number.flow_daily_goal
         - input_number.flow_time_tracked_today
         - input_number.flow_streak_counter
   ```
2. Consider using the [History Stats](https://www.home-assistant.io/integrations/history_stats/) integration for long-term tracking

### Docker-Specific Issues

1. **File Permissions**: Ensure the `packages` directory is accessible
   - Files should be owned by the user running Home Assistant container
   - Typical UID: `1000:1000` or the user specified in your Docker setup

2. **Volume Mounts**: Verify your configuration directory is properly mounted
   - Check your `docker-compose.yml` or docker run command
   - Ensure `/config` is mapped to your host directory

3. **Time Zone**: Ensure container has correct time zone
   - Add environment variable: `TZ=Your/Timezone`
   - Example: `TZ=Europe/Berlin` or `TZ=America/New_York`

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Report Bugs**: Open an issue with details and reproduction steps
2. **Suggest Features**: Share your ideas in the issues section
3. **Submit Pull Requests**: 
   - Fork the repository
   - Create a feature branch
   - Make your changes
   - Submit a PR with a clear description

Please ensure:
- All changes work via Web UI
- Documentation is updated
- YAML is properly formatted
- Docker compatibility is maintained

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Home Assistant Community** - For the amazing platform
- **[@valentin-berlin](https://github.com/valentin-berlin/)** - For the original idea
- **All Contributors** - For improvements and feedback

---

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/v-berlin/Flow/issues)
- **Discussions**: [GitHub Discussions](https://github.com/v-berlin/Flow/discussions)
- **Home Assistant Community**: [Community Forum](https://community.home-assistant.io/)

---

<div align="center">

**Built with ❤️ for the Home Assistant Community**

[⬆ Back to Top](#flow-for-home-assistant)

</div>
