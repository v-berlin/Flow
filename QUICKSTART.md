# Flow - Quick Start Guide

**Get up and running with Flow in 15 minutes!**

---

## Prerequisites

✅ Home Assistant installed (Docker or any setup)  
✅ Access to Home Assistant Web UI  
✅ 15 minutes of your time  

---

## Part 1: Create Helpers (10 minutes)

Go to: **Settings** → **Devices & Services** → **Helpers** tab

### Create 3 Numbers:

1. **+ CREATE HELPER** → **Number**
   - Name: `Flow: Daily Goal`, Icon: `mdi:target`, Min: `0`, Max: `1440`, Step: `5`, Unit: `min`
   
2. **+ CREATE HELPER** → **Number**
   - Name: `Flow: Time Tracked Today`, Icon: `mdi:clock-check`, Min: `0`, Max: `1440`, Step: `1`, Unit: `min`
   
3. **+ CREATE HELPER** → **Number**
   - Name: `Flow: Streak Days`, Icon: `mdi:fire`, Min: `0`, Max: `9999`, Step: `1`

### Create 7 Toggles:

4. **+ CREATE HELPER** → **Toggle**
   - Name: `Flow: Timer Active`, Icon: `mdi:play-pause`
   
5. **+ CREATE HELPER** → **Toggle**
   - Name: `Flow: Sick Mode`, Icon: `mdi:hospital-box`
   
6-10. **+ CREATE HELPER** → **Toggle** (repeat 5 times)
   - `Flow: Work` (Icon: `mdi:briefcase`)
   - `Flow: Personal` (Icon: `mdi:home`)
   - `Flow: Coding` (Icon: `mdi:code-braces`)
   - `Flow: Design` (Icon: `mdi:palette`)
   - `Flow: Other` (Icon: `mdi:circle-outline`)

### Create 1 Date/Time:

11. **+ CREATE HELPER** → **Date and/or time**
    - Name: `Flow: Timer Start Time`, Has date: ✅, Has time: ✅

### Create 1 Text:

12. **+ CREATE HELPER** → **Text**
    - Name: `Flow: Active Category`, Max length: `20`, Initial: `None`

### Create 4 Template Sensors:

13. **+ CREATE HELPER** → **Template** → **Template a sensor**
    - Name: `Flow: Goal Progress`
    - State template: (Copy from README.md section 1.5)
    - Unit: `%`
    - Icon: `mdi:percent-circle`

14-16. Repeat for:
    - `Flow: Status`
    - `Flow: Active Work`
    - `Flow: Timer Elapsed`

(Full template code is in README.md)

---

## Part 2: Create Automations (4 minutes)

Go to: **Settings** → **Automations & Scenes** → **Automations**

### Create 4 Automations:

For each automation:
1. **+ CREATE AUTOMATION** → **Create new automation**
2. Click **⋮** (three dots) → **Edit in YAML**
3. Paste the YAML from README.md
4. Click **SAVE**

Automations to create:
1. Flow: Timer Toggle Handler
2. Flow: Category Selection
3. Flow: Daily Reset
4. Flow: Goal Reached Notification

(Full YAML code is in README.md section 2.1-2.4)

---

## Part 3: Create Dashboard (1 minute)

Go to: **Settings** → **Dashboards** → **+ ADD DASHBOARD**

1. Name: `Flow Productivity`
2. Click **CREATE**
3. Click **✏️ Edit**
4. **+ ADD CARD** → **Manual**
5. Paste the dashboard YAML from README.md section "Step 3"
6. Click **SAVE**

---

## Part 4: Start Using Flow!

### First Time Setup:

1. Go to your new "Flow Productivity" dashboard
2. Set your daily goal (e.g., 240 minutes = 4 hours)
3. Select a category (tap one of the buttons: Work, Personal, etc.)
4. Toggle the Timer to ON
5. Work on your task!
6. Toggle the Timer to OFF when done

### Daily Workflow:

```
1. Select Category → 2. Start Timer → 3. Work → 4. Stop Timer → 5. Repeat
```

At midnight, if you reached your goal, your streak increases! 🔥

---

## Troubleshooting

**"Template sensors show Unknown"**
→ Wait 2-3 minutes or restart Home Assistant

**"Timer doesn't track time"**
→ Check that "Flow: Timer Toggle Handler" automation is enabled

**"Can't select multiple categories"**
→ This is correct! Only one category should be active at a time

**"Need help!"**
→ Check the full README.md for detailed troubleshooting

---

## Next Steps

- Read README.md for detailed information
- Customize categories to match your work style
- Adjust your daily goal as needed
- Share your progress with friends!

---

**That's it! You're ready to Flow! 🚀**
