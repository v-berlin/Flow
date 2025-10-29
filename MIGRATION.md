# Migration Guide: Motive → Flow

This guide helps you migrate from the old "Motive" system to the new streamlined "Flow" system.

## What Changed?

### Summary of Changes
- **Project Name**: Motive → Flow
- **Entities**: 24 → 17 (29% reduction)
- **Automations**: 5 → 4 (more efficient)
- **Scripts**: 6 → 4 (simplified)
- **Code Quality**: Improved readability and maintainability
- **Documentation**: Completely rewritten with Web UI-only setup

### Removed Features
The following entities were removed as they were redundant or unused:
- History tracking (complex JSON storage)
- Timer entity (wasn't actually used for timing)
- Daily achievement storage
- Session minutes counter
- Weekly activity history stats

### Renamed Entities
All entities follow this pattern:
- `input_number.motive_*` → `input_number.flow_*`
- `input_boolean.motive_*` → `input_boolean.flow_*`
- `input_datetime.motive_*` → `input_datetime.flow_*`
- `input_text.motive_*` → `input_text.flow_*`
- `sensor.motive_*` → `sensor.flow_*`
- `script.motive_*` → `script.flow_*`

### Language Changes
- "Privat" → "Personal" (English consistency)
- "Keine" → "None" (English consistency)
- German notifications → English notifications

## Migration Steps

### Option 1: Clean Installation (Recommended)

This is the easiest approach and gives you a fresh start.

1. **Backup Your Current Data**
   - Note your current streak count
   - Note your daily goal
   - Save any important statistics

2. **Remove Old Motive Configuration**
   - In File Editor, delete `packages/motive.yaml`
   - In `automations.yaml`, remove all Motive automations (look for IDs starting with the old IDs)
   - In `scripts.yaml`, remove all Motive scripts

3. **Install Flow**
   - Follow the [Installation Guide](README.md#installation) in the README
   - Set your daily goal to your previous value
   - Optionally, manually set your streak counter to continue your streak

4. **Restart Home Assistant**
   - Settings → System → Restart

5. **Clean Up Old Entities** (Optional)
   - Go to Settings → Devices & Services → Entities
   - Filter for "motive"
   - Delete any remaining Motive entities

### Option 2: Side-by-Side (For Testing)

Run both systems temporarily to test Flow before removing Motive.

1. **Keep Existing Motive**
   - Don't delete anything yet

2. **Add Flow**
   - Follow the [Installation Guide](README.md#installation)
   - Flow entities will coexist with Motive entities

3. **Test Flow**
   - Create a test dashboard for Flow
   - Use Flow for a day or two
   - Verify everything works

4. **Switch to Flow**
   - Once satisfied, follow Option 1 steps 2-5 to remove Motive

### Option 3: In-Place Upgrade (Advanced)

Replace Motive files directly. **Note**: This will lose your current day's data.

1. **Backup Everything**
   ```
   Settings → System → Backups → Create Backup
   ```

2. **Replace Package File**
   - In File Editor, open `packages/motive.yaml`
   - Delete all contents
   - Paste the entire contents of `flow.yaml`
   - Save

3. **Replace Automations**
   - Open `automations.yaml`
   - Find and delete all Motive automations
   - Paste the new Flow automations from this repository
   - Save

4. **Replace Scripts**
   - Open `scripts.yaml`
   - Find and delete all Motive scripts
   - Paste the new Flow scripts from this repository
   - Save

5. **Check Configuration**
   - Settings → System → Check Configuration

6. **Restart Home Assistant**
   - Settings → System → Restart

7. **Reconfigure Initial Values**
   - Set your daily goal
   - Manually restore your streak if desired

## Data Migration

### Preserving Your Streak

If you want to keep your streak count:

1. **Before Migration**:
   - Note the value of `input_number.motive_streak_counter`

2. **After Migration**:
   - Go to Developer Tools → Services
   - Service: `input_number.set_value`
   - Entity: `input_number.flow_streak_counter`
   - Value: Your noted streak count
   - Call Service

### Preserving Your Daily Goal

Your daily goal can be transferred the same way:

1. **Before Migration**:
   - Note the value of `input_number.motive_daily_goal`

2. **After Migration**:
   - Go to Developer Tools → Services
   - Service: `input_number.set_value`
   - Entity: `input_number.flow_daily_goal`
   - Value: Your noted goal in minutes
   - Call Service

### Today's Tracked Time

**Warning**: Current day's tracked time cannot be easily migrated. Plan your migration for the start of a new day (after midnight) to avoid losing progress.

## Dashboard Migration

Your old Motive dashboard will not automatically work with Flow. You need to update it:

1. **Open Your Dashboard**
   - Go to your dashboard with Motive cards
   - Click Edit Dashboard (three dots → Edit Dashboard)

2. **Update Each Card**
   - For each Motive card, click the pencil icon
   - Find entity references (e.g., `input_number.motive_daily_goal`)
   - Change `motive` to `flow`
   - Save the card

3. **Or Create New Dashboard**
   - Follow the [Dashboard Setup guide](README.md#dashboard-setup) in the README
   - Copy the provided YAML configuration
   - This gives you a fresh, optimized layout

## Troubleshooting

### Entities Not Appearing

- **Check**: Did you restart Home Assistant?
- **Check**: Is `packages: !include_dir_named packages` in configuration.yaml?
- **Check**: Is the file named exactly `flow.yaml` in the `packages` folder?
- **Solution**: Settings → System → Logs to check for errors

### Old Entities Still Showing

- **Normal**: Old Motive entities remain after migration
- **Solution**: Manually delete them via Settings → Devices & Services → Entities

### Automations Not Working

- **Check**: Are the new automations enabled?
- **Check**: Settings → Automations & Scenes → Look for Flow automations
- **Solution**: Click on each automation and ensure the toggle is ON

### Dashboard Showing "Unavailable"

- **Cause**: Dashboard still references old `motive_*` entities
- **Solution**: Edit each card and change entity references to `flow_*`

### Configuration Check Fails

- **Check**: YAML syntax errors
- **Solution**: Use File Editor's built-in YAML validation
- **Solution**: Compare your files to the repository files character-by-character

## Rollback Plan

If something goes wrong, you can rollback:

1. **Restore from Backup**
   - Settings → System → Backups
   - Find your pre-migration backup
   - Click Restore

2. **Or Manually Restore**
   - Remove Flow files (flow.yaml, new automations, new scripts)
   - Restore old Motive files from your backup
   - Restart Home Assistant

## Post-Migration Checklist

After migrating, verify:

- [ ] All Flow entities are visible in Settings → Devices & Services → Entities
- [ ] Timer toggle works (start/stop)
- [ ] Category selection works (only one active at a time)
- [ ] Time tracking accumulates correctly
- [ ] Dashboard displays properly
- [ ] Notifications appear when timer starts/stops
- [ ] Daily goal is set correctly
- [ ] Streak counter is preserved (if applicable)

## Need Help?

- **Issues**: [GitHub Issues](https://github.com/v-berlin/Flow/issues)
- **Discussions**: [GitHub Discussions](https://github.com/v-berlin/Flow/discussions)
- **Community**: [Home Assistant Community Forum](https://community.home-assistant.io/)

---

**Note**: It's recommended to perform the migration at the start of a new day (after midnight reset) to avoid losing your current day's tracked time.
