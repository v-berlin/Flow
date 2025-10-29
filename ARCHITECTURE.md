# Flow Architecture

This document explains how Flow works internally and how all components interact.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                         USER INTERFACE                       │
│                      (Dashboard / Mobile)                    │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      CONTROL ENTITIES                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Timer Toggle │  │  Categories  │  │  Sick Mode   │      │
│  │ (Boolean)    │  │ (5 Booleans) │  │  (Boolean)   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                        AUTOMATIONS                           │
│  ┌──────────────────────────────────────────────────┐       │
│  │ 1. Timer Toggle Handler                          │       │
│  │    - Start: Record start time                    │       │
│  │    - Stop: Calculate elapsed time, add to total  │       │
│  └──────────────────────────────────────────────────┘       │
│  ┌──────────────────────────────────────────────────┐       │
│  │ 2. Category Selection                            │       │
│  │    - Ensure only one category active             │       │
│  │    - Update active category text                 │       │
│  └──────────────────────────────────────────────────┘       │
│  ┌──────────────────────────────────────────────────┐       │
│  │ 3. Daily Reset (Midnight)                        │       │
│  │    - Check if goal reached                       │       │
│  │    - Update streak counter                       │       │
│  │    - Reset daily counters                        │       │
│  └──────────────────────────────────────────────────┘       │
│  ┌──────────────────────────────────────────────────┐       │
│  │ 4. Goal Reached Notification                     │       │
│  │    - Trigger when progress > 99.9%               │       │
│  │    - Send celebration notification               │       │
│  └──────────────────────────────────────────────────┘       │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      DATA STORAGE                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Daily Goal   │  │Time Tracked  │  │Streak Counter│      │
│  │  (Number)    │  │  (Number)    │  │  (Number)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐                        │
│  │ Timer Start  │  │Active Category│                       │
│  │  (DateTime)  │  │   (Text)     │                        │
│  └──────────────┘  └──────────────┘                        │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    TEMPLATE SENSORS                          │
│  ┌──────────────────────────────────────────────────┐       │
│  │ Goal Progress (%)                                │       │
│  │ = (time_tracked / daily_goal) × 100              │       │
│  └──────────────────────────────────────────────────┘       │
│  ┌──────────────────────────────────────────────────┐       │
│  │ Status (Text)                                    │       │
│  │ = Current activity status message                │       │
│  └──────────────────────────────────────────────────┘       │
│  ┌──────────────────────────────────────────────────┐       │
│  │ Active Work (Text)                               │       │
│  │ = Current category if timer active               │       │
│  └──────────────────────────────────────────────────┘       │
│  ┌──────────────────────────────────────────────────┐       │
│  │ Timer Elapsed (Minutes)                          │       │
│  │ = (now - timer_start) / 60                       │       │
│  └──────────────────────────────────────────────────┘       │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    VISUALIZATION                             │
│              (Dashboard displays all data)                   │
└─────────────────────────────────────────────────────────────┘
```

---

## Data Flow

### 1. Starting a Timer Session

```
User selects category (e.g., "Work")
    ↓
Category Selection Automation triggers
    ↓
All other categories turned OFF
    ↓
Active Category Text updated to "Work"
    ↓
User toggles Timer to ON
    ↓
Timer Toggle Handler Automation triggers
    ↓
Timer Start DateTime set to NOW
    ↓
Notification: "Timer Started - Category: Work"
    ↓
Template Sensors begin calculating elapsed time
    ↓
Dashboard shows timer running
```

### 2. Stopping a Timer Session

```
User toggles Timer to OFF
    ↓
Timer Toggle Handler Automation triggers
    ↓
Calculate: elapsed = NOW - timer_start_time
    ↓
Convert to minutes
    ↓
Add elapsed minutes to time_tracked_today
    ↓
Notification: "Timer Stopped - X minutes tracked"
    ↓
Template sensors update
    ↓
Dashboard shows updated progress
```

### 3. Daily Reset at Midnight

```
Midnight (00:00:00) arrives
    ↓
Daily Reset Automation triggers
    ↓
Check: time_tracked >= daily_goal?
    ├─ YES → Increment streak_counter
    │         Send "Streak!" notification
    ├─ SICK MODE ON → Keep streak unchanged
    │                  Send "Sick Day" notification
    └─ NO → Reset streak_counter to 0
            Send "Streak Lost" notification
    ↓
Reset time_tracked_today to 0
    ↓
Turn OFF all toggles (timer, sick mode, categories)
    ↓
Reset active_category to "None"
    ↓
Ready for a new day!
```

### 4. Reaching Daily Goal

```
Time tracked increases
    ↓
Template sensor: goal_progress calculates percentage
    ↓
Progress reaches 100%+
    ↓
Goal Reached Notification Automation triggers
    ↓
Check: Not in sick mode + Last trigger > 20h ago
    ↓
Send "🎉 Daily Goal Reached!" notification
```

---

## Entity Relationships

### Input Numbers (Storage)
```
flow_daily_goal (0-1440 min)
    ↓
    └─→ Used by: goal_progress calculation
                 Daily Reset logic

flow_time_tracked_today (0-1440 min)
    ↓
    ├─→ Updated by: Timer Toggle Handler (on stop)
    ├─→ Reset by: Daily Reset (at midnight)
    └─→ Used by: goal_progress calculation

flow_streak_counter (0-9999 days)
    ↓
    ├─→ Updated by: Daily Reset
    └─→ Displayed on: Dashboard
```

### Input Booleans (Controls)
```
flow_timer_active (ON/OFF)
    ↓
    ├─→ Triggers: Timer Toggle Handler
    └─→ Controls: Timer state

flow_cat_* (5 categories)
    ↓
    ├─→ Trigger: Category Selection
    └─→ One active at a time

flow_sick_mode (ON/OFF)
    ↓
    └─→ Affects: Daily Reset logic
```

### Input DateTime (Timestamp)
```
flow_timer_start
    ↓
    ├─→ Set by: Timer Toggle Handler (on start)
    └─→ Used by: timer_elapsed calculation
```

### Input Text (State)
```
flow_active_category
    ↓
    ├─→ Updated by: Category Selection
    └─→ Displayed in: Status sensor, notifications
```

### Template Sensors (Calculated)
```
flow_goal_progress
    = (time_tracked_today / daily_goal) × 100

flow_status
    = Dynamic message based on timer state

flow_active_work
    = active_category (if timer ON) or "Inactive"

flow_timer_elapsed
    = (now - timer_start) in minutes (if timer ON)
```

---

## Automation Triggers

| Automation | Trigger Type | When |
|-----------|--------------|------|
| Timer Toggle Handler | State Change | flow_timer_active changes |
| Category Selection | State Change | Any category toggle turns ON |
| Daily Reset | Time | 00:00:00 every day |
| Goal Reached | Numeric State | goal_progress > 99.9% |

---

## State Machine

### Timer States
```
┌──────────┐
│  IDLE    │ ← Timer OFF, No category selected
│ (Start)  │
└────┬─────┘
     │ User selects category
     ▼
┌──────────┐
│  READY   │ ← Category selected, Timer OFF
└────┬─────┘
     │ User starts timer
     ▼
┌──────────┐
│ RUNNING  │ ← Timer ON, Category active, Time accumulating
└────┬─────┘
     │ User stops timer
     ▼
┌──────────┐
│ STOPPED  │ ← Timer OFF, Time added to total
└────┬─────┘
     │ Can repeat cycle or select new category
     ▼
   (READY)
```

### Streak States
```
┌──────────────┐
│ Day Starts   │
│ Streak = N   │
└──────┬───────┘
       │
       ▼
  Midnight Reset
       │
       ├─ Goal Reached? ──YES──→ Streak = N+1 ──┐
       │                                         │
       ├─ Sick Mode? ────YES──→ Streak = N ─────┤
       │                                         │
       └─ Neither ────────────→ Streak = 0 ─────┘
                                                 │
                                                 ▼
                                            Continue...
```

---

## Performance Considerations

### Template Sensor Updates
- **Goal Progress**: Updates whenever `time_tracked_today` or `daily_goal` changes
- **Status**: Updates on timer state, sick mode, or progress changes
- **Active Work**: Updates when timer or category changes
- **Timer Elapsed**: Updates every minute while timer is running

### Automation Efficiency
- **Timer Toggle Handler**: Single-mode, processes one state change at a time
- **Category Selection**: Instantly deselects other categories
- **Daily Reset**: Runs once per day, minimal resource usage
- **Goal Reached**: Throttled to once per 20 hours

---

## Error Handling

### Missing Data
```yaml
{% set goal = states('input_number.flow_daily_goal') | float(0) %}
#                                                      ^^^^^^^^
#                                                      Default to 0 if unavailable
```

### Division by Zero
```yaml
{% if goal > 0 %}
  {{ (tracked / goal) * 100 }}
{% else %}
  0
{% endif %}
```

### Invalid Timestamps
```yaml
{% set start = states('input_datetime.flow_timer_start') | as_datetime %}
{% if start %}
  # Calculate elapsed time
{% else %}
  0
{% endif %}
```

---

## Extensibility

Flow is designed to be extended without modifying core functionality:

### Adding New Categories
1. Create new `input_boolean.flow_cat_*`
2. Add to Category Selection automation
3. Add to Daily Reset automation
4. Add to dashboard

### Adding Statistics
1. Create new template sensors
2. Reference existing data (time_tracked, categories)
3. Display on custom dashboard cards

### Integration with Other Systems
1. Use Flow's entities in other automations
2. Send data to external services
3. Create custom notifications

---

## Security & Privacy

- **All data stored locally** in Home Assistant database
- **No external services** required
- **No personal data collection**
- **User controls all data** through Home Assistant

---

## Backup & Recovery

### What to Backup
- All helper entities (automatically in HA backup)
- All automations (automatically in HA backup)
- Dashboard configuration (automatically in HA backup)

### Recovery Process
1. Restore Home Assistant backup
2. All Flow entities and automations restored
3. Historical data restored from HA database

---

## Future Optimization Possibilities

1. **Reduce template calculations** - Cache calculated values
2. **Event-driven updates** - Update only when necessary
3. **Batch operations** - Group related entity updates
4. **History compression** - Store long-term data more efficiently

However, current performance is excellent for typical use cases.

---

<div align="center">

**Flow Architecture: Simple, Efficient, Reliable**

</div>
