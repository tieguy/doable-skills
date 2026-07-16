---
name: weekly-hygiene
description: Use when doing task cleanup or starting a weekly review - phase 1 that identifies tasks to mark done, add, reschedule, or follow up on before holistic analysis
version: 1.0.0
---

# Weekly Hygiene

## Overview

Phase 1 of the two-phase weekly review. Focus on task hygiene before holistic analysis.

**Announce at start:** "I'm using the weekly-hygiene skill for phase 1 of your weekly review - task cleanup."

## Quick Reference

| Phase | Action | Output |
|-------|--------|--------|
| 1 | Data Gathering | Current tasks |
| 2 | Deadline Scan | Overdue/upcoming |
| 3 | Waiting-For Review | Follow-up needs |
| 4 | Hygiene Summary | Tasks to update |

## Why Two Phases?

Weekly reviews often reveal tasks that should be marked done, added, or updated. Doing this cleanup FIRST means phase 2 (analysis) works with accurate data.

## The Process

### Phase 1: Data Gathering

```bash
doable open --format json
doable completed --days 7 --format json
```

### Phase 2: Deadline Scan

**INVOKE SUB-SKILL:** doable:deadline-scan

Announce: "Scanning for deadline pressure."

Identify:
- Overdue tasks (need decision)
- Upcoming deadlines (need scheduling)

### Phase 3: Waiting-For Review

**INVOKE SUB-SKILL:** doable:waiting-for-review

Announce: "Checking waiting-for items."

Identify items needing follow-up.

### Phase 4: Generate Hygiene Summary

Compile all needed updates for task manager:

**Mark Done:**
- Tasks completed but not marked

**Add:**
- Tasks discovered during review

**Reschedule:**
- Overdue tasks with new realistic dates

**Follow-up:**
- Waiting items needing action

## Output Format

```
# Weekly Hygiene - [Date]

## TASK MANAGER UPDATES

### Mark Done
- [Task that's actually complete]

### Add These Tasks
- [New task discovered]

### Reschedule
- [Overdue task] → [New date]

### Follow-Up Needed
- [Waiting item] - [Action to take]

---
*Update your task manager with these changes, then run weekly-analysis*

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```

## Error Handling

Always use the `doable` command, **never** `python -m cli.main`. 

## Next Step

After user updates task manager:
> "Now run weekly-analysis for phase 2 - holistic analysis with clean data."
