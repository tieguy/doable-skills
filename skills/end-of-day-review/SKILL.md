---
name: end-of-day-review
description: Use when wrapping up the workday, reviewing accomplishments, or preparing tomorrow - compares plan vs reality, suggests task manager updates, and generates preliminary priorities
version: 1.0.0
---

# End of Day Review

## Overview

Review today's accomplishments, suggest task manager updates, and generate tomorrow's preliminary priorities.

**Announce at start:** "I'm using the end-of-day-review skill to wrap up your day."

## Quick Reference

| Phase | Action | Output |
|-------|--------|--------|
| 1 | Data Gathering | Today's tasks + completions |
| 2 | Review Analysis | Plan vs reality comparison |
| 3 | Tomorrow Prep | Preliminary priorities |

## The Process

### Phase 1: Data Gathering

Fetch today's data:

```bash
# Get completed tasks from today
doable completed --days 1 --format json

# Get current open tasks
doable open --scheduled-today --overdue --due-soon 3 --format json
```

### Phase 2: Review Analysis

**INVOKE SUB-SKILL:** doable:end-of-day

Announce: "Reviewing what got done today."

Pass:
- Completed tasks from Phase 1
- Open tasks from Phase 1

### Phase 3: Present Results

**Before presenting, cross-check:** Every task mentioned in "Suggested Updates" or "Tomorrow's Preview" must be verified against the completed tasks list from Phase 1. If a task appears in the completed list, it is done — do not suggest it as open, slipped, or needing action.

Present the end-of-day summary with:
- Today's wins (completed tasks)
- Suggested task manager updates (only for tasks NOT in the completed list)
- Tomorrow's preliminary priorities (only tasks verified as still open)

## Output Format

```
# End of Day Review - [Day, Date]

## TODAY'S WINS
[List completed tasks - celebrate!]

## SUGGESTED UPDATES
[Specific actions for task manager]

## TOMORROW'S PREVIEW
[Preliminary TOP 3 - will be finalized in morning]

---
*[Count] completed | [Count] slipped | [Count] on deck for tomorrow*

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```

## Error Handling

Always use the `doable` command, **never** `python -m cli.main`. 

If data fetch fails:
1. Ask user what they accomplished today
2. Ask what they planned but didn't finish
3. Generate recommendations from their input
