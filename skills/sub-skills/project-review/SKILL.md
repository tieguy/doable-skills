---
name: project-review
description: Use when assessing project health - classifies as WAITING, ACTIVE, INCOMPLETE, or STALLED and recommends actions for each
version: 1.0.0
---

# Project Review

## Purpose

Ensure every project has a clear next action and none are stalled. Core GTD weekly review component.

## Input

Expects projects grouped by health status from calling skill:
- **WAITING**: Has next actions, all marked "waiting"
- **ACTIVE**: Has completions in last 7 days
- **INCOMPLETE**: No next actions defined (violates GTD)
- **STALLED**: Has next actions but no completions

## Process

### Review in Order

**1. WAITING Projects**
- Verify waiting is still appropriate
- Check if follow-ups needed
- Note any that should move to active

**2. ACTIVE Projects**
- Celebrate progress (mention specific completions)
- Ensure momentum continues
- Check for blockers

**3. INCOMPLETE Projects** (GTD Violation!)
- MUST define at least one next action
- Suggest concrete, actionable steps

**4. STALLED Projects** (Decision Required)
For each stalled project, recommend one of:
- **Activate**: Define better/different next action
- **Defer**: Move to someday/maybe
- **Abandon**: Close project entirely

## Output

```
### PROJECT HEALTH SUMMARY

**WAITING** (N projects) - Appropriately blocked
- [Project] - Waiting for [what]

**ACTIVE** (N projects) - Making progress
- [Project] - Completed [X] this week

**INCOMPLETE** (N projects) - ⚠️ Needs next action!
- [Project] → Suggested next action: [specific task]

**STALLED** (N projects) - ⚠️ Decision needed
- [Project] - Recommend: [Activate/Defer/Abandon] because [reason]

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```

## Key Principles

- Every project MUST have a next action (GTD core)
- Be specific about what's blocking stalled projects
- Celebrate wins for active projects
