---
name: weekly-analysis
description: Use after weekly-hygiene is complete and task manager updated - phase 2 that assesses project health, category balance, and backburner items for strategic insights
version: 1.0.0
---

# Weekly Analysis

## Overview

Phase 2 of the two-phase weekly review. Holistic analysis after task hygiene is complete.

**Announce at start:** "I'm using the weekly-analysis skill for phase 2 of your weekly review - holistic analysis."

**Prerequisite:** Run weekly-hygiene first and update task manager.

## Quick Reference

| Phase | Action | Output |
|-------|--------|--------|
| 1 | Data Gathering | Fresh task data |
| 2 | Project Review | Health assessment |
| 3 | Category Health | Distribution analysis |
| 4 | Someday/Maybe | Backburner triage |
| 5 | Weekly Summary | Strategic insights |

## The Process

### Phase 1: Data Gathering (Fresh)

```bash
doable open --format json
doable projects --format json
```

### Phase 2: Project Review

**INVOKE SUB-SKILL:** doable:project-review

Announce: "Reviewing project health."

Assess each project's status:
- WAITING, ACTIVE, INCOMPLETE, STALLED

### Phase 3: Category Health

**INVOKE SUB-SKILL:** doable:category-health

Announce: "Analyzing category distribution."

Check for overloaded/neglected areas.

### Phase 4: Someday/Maybe Review

**INVOKE SUB-SKILL:** doable:someday-maybe-review

Announce: "Triaging backburner items."

Decide: activate, keep, or remove.

### Phase 5: Weekly Summary

Synthesize insights from all sub-skills.

## Output Format

```
# Weekly Analysis - [Date]

## PROJECT HEALTH
[From project-review sub-skill]
- ACTIVE: [N] projects making progress
- STALLED: [N] projects need decisions
- INCOMPLETE: [N] projects need next actions

## CATEGORY BALANCE
[From category-health sub-skill]
- Overloaded: [areas]
- Neglected: [areas]

## BACKBURNER TRIAGE
[From someday-maybe sub-skill]
- [N] activated, [N] kept, [N] removed

## THIS WEEK'S FOCUS
Based on analysis, prioritize:
1. [Top priority for the week]
2. [Second priority]
3. [Third priority]

## WINS THIS WEEK
- [Completed items worth celebrating]

---
*Weekly review complete. Set intentions for next week.*

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```

## Error Handling

Always use the `doable` command, **never** `python -m cli.main`. 

## Key Principles

- This runs AFTER hygiene - data should be clean
- Focus on strategic insights, not task-by-task
- End on wins and clear priorities
