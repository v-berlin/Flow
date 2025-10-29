# Flow Quick Start Guide

Get Flow up and running in **10 minutes** using only the Home Assistant Web UI.

## Prerequisites

- ✅ Home Assistant running in Docker
- ✅ Access to Home Assistant Web UI
- ✅ Basic familiarity with Home Assistant

## Installation (5 Steps)

### 1. Enable File Editor

1. Settings → Add-ons → Add-on Store
2. Search for "File editor" → Install
3. Start the add-on
4. Enable "Show in sidebar"

### 2. Enable Packages

1. Click File Editor in sidebar
2. Open `configuration.yaml`
3. Add under `homeassistant:`:
   ```yaml
   packages: !include_dir_named packages
   ```
4. Save (disk icon)

### 3. Create Package Structure

1. In File Editor, click folder+ icon
2. Create folder: `packages`
3. Click on `packages` folder
4. Click file+ icon
5. Create file: `flow.yaml`
6. Paste contents from repository's `flow.yaml`
7. Save

### 4. Add Automations & Scripts

**Automations:**
1. Open `automations.yaml`
2. Paste contents from repository's `automations.yaml`
3. Save

**Scripts:**
1. Open `scripts.yaml`
2. Paste contents from repository's `scripts.yaml`
3. Save

### 5. Restart Home Assistant

1. Settings → System → Restart
2. Check Configuration first (green checkmark)
3. Click Restart Home Assistant
4. Wait 1-2 minutes

**Done! Flow is installed.**

## Configuration (2 Minutes)

### Set Your Daily Goal

1. Settings → Devices & Services → Entities
2. Search: `flow_daily_goal`
3. Click the entity
4. Set value (e.g., 240 for 4 hours)
5. Update

## Create Dashboard (3 Minutes)

1. Overview → ⋯ (three dots) → Edit Dashboard
2. + Add Card → Manual (bottom)
3. Paste this YAML:

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
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_number.flow_time_tracked_today
        name: Tracked
        color: blue
      
      - type: tile
        entity: input_number.flow_streak_counter
        name: Streak
        color: orange
  
  - type: tile
    entity: input_boolean.flow_timer_active
    name: Timer
    color: red
    hide_state: true
    features:
      - type: toggle
  
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

4. Save → Done

**Your dashboard is ready!**

## First Use

### Start Tracking Time

1. **Choose a category**: Tap one category button (e.g., "Work")
2. **Start timer**: Toggle "Timer" to ON
3. **Work**: Do your task
4. **Stop timer**: Toggle "Timer" to OFF

Your time is automatically tracked and added to today's total!

## Understanding the Dashboard

| Card | What It Shows |
|------|---------------|
| **Progress** | How close you are to your daily goal (%) |
| **Tracked** | Minutes you've worked today |
| **Streak** | Consecutive days you've hit your goal |
| **Timer** | Start/Stop tracking |
| **Categories** | What you're working on |
| **Sick Mode** | Pause streaks when unwell |

## Daily Workflow

1. **Morning**: Check your goal and streak
2. **Work Session**: 
   - Select category
   - Toggle timer ON
   - Work on task
   - Toggle timer OFF
3. **Throughout Day**: Repeat step 2 for each work session
4. **Evening**: Check if you reached your goal
5. **Midnight**: Automatic reset
   - Hit goal? → Streak +1 ✅
   - Sick mode? → Streak preserved 🏥
   - Missed goal? → Streak resets to 0 ❌

## Tips

- 💡 **Categories**: Use them to track what type of work you do most
- 🏥 **Sick Mode**: Turn ON before midnight if you're unwell
- 🎯 **Adjust Goal**: Start small (60-120 min) and increase gradually
- 📱 **Mobile**: Access from Home Assistant mobile app too
- 🔔 **Notifications**: You'll get notifications when reaching goals

## Troubleshooting

**Entities not showing?**
- Check: Settings → System → Logs
- Fix: Verify YAML syntax in files
- Restart Home Assistant again

**Timer not tracking?**
- Ensure a category is selected first
- Check automation is enabled: Settings → Automations & Scenes

**Dashboard not working?**
- Verify all entity names match (should start with `flow_`)
- Check for typos in YAML

## Next Steps

- ✅ Read full [README.md](README.md) for detailed documentation
- ✅ Join discussions on [GitHub](https://github.com/v-berlin/Flow)
- ✅ Customize categories and goals to fit your workflow
- ✅ Track your productivity and build streaks!

---

**Ready to flow? Start tracking now!** 🚀
