---
name: end-of-day
description: Use when comparing today's plan vs reality - identifies wins, slipped tasks, and generates preliminary tomorrow priorities
version: 1.0.0
---

# End of Day Review

## Purpose

Review what was accomplished today, identify updates needed in task manager, and generate tomorrow's priority list. This is a sub-skill called by orchestrating skills.

## Input

Expects from calling skill:
- Today's scheduled tasks (from morning plan)
- Completed tasks (from doable completed --days 1)
- Current open tasks

## Process

### Step 1: Compare Plan vs Reality

1. **What was planned?** List morning's TOP 3 and scheduled tasks
2. **What was completed?** Check completed tasks for today
3. **What slipped?** Planned but not done

### Step 2: Generate Task Manager Updates

**CRITICAL: Cross-reference before suggesting.** Before recommending ANY update, check it against the completed tasks list from Phase 1. If a task appears in the completed list, it is done — do not suggest marking it done, rescheduling it, or acting on it in any way.

Recommend updates for user's task manager:

| Update Type | Examples |
|-------------|----------|
| Mark Done | Tasks completed but not yet marked **in a specific backend** (verify the task is NOT already in the completed list) |
| Add New | Tasks discovered during day |
| Reschedule | Slipped tasks need new dates |
| Delete | Tasks no longer relevant |

### Step 3: Tomorrow's Priorities

**CRITICAL: Filter against completed tasks.** Before presenting ANY task as a tomorrow priority, verify it does NOT appear in the completed tasks list. A task that was completed today is not a priority for tomorrow.

Based on:
- Slipped tasks from today that are NOT in the completed list (high priority)
- Due tomorrow or this week, NOT already completed
- Strategic importance

Generate preliminary TOP 3 for tomorrow. Every suggested task must have been verified as still open.

## Output

Return to calling skill:

```
### TODAY'S WINS
- {title} ({project})

### SUGGESTED UPDATES FOR [TASK MANAGER]
**Mark Done:**
- {title} ({project})

**Add:**
- [New task description]

**Reschedule:**
- {title} ({project}) → [Suggested new date]

### TOMORROW'S PRELIMINARY PRIORITIES
1. {title} ({project}) - [Why]
2. {title} ({project}) - [Why]
3. {title} ({project}) - [Why]

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**

*Note: These will be finalized in tomorrow's morning review after overnight changes*
```

## Key Principles

- **Celebrate wins** - Acknowledge what got done
- **Be honest** - If nothing got done, say so
- **Don't pile on** - Slipped tasks happen, reschedule don't guilt
- **Tomorrow is fresh** - Morning review will finalize
