---
name: weekly-analysis
description: Use after weekly-hygiene is complete - phase 2 that assesses project health, area balance, and the Someday-Maybe tree for strategic insights via the Todoist connector
version: 2.0.0
---

# Weekly Analysis

## Overview

Phase 2 of the two-phase weekly review: holistic analysis after task hygiene is complete. **Prerequisite:** weekly-hygiene has run and its updates are applied.

Self-contained: uses the **Todoist connector/MCP tools directly**. No CLI, no other skills, no filesystem. Tool names vary slightly by surface — locate tools by name fragment rather than assuming exact names.

**Todoist tool cautions (observed bugs):**
- Fetch dated tasks with `find-tasks` + a filter query. **Avoid `find-tasks-by-date`** — with `daysCount` > 1 it has silently dropped tasks actually due today. If it is the only such tool available, also run it with `daysCount=1` for today and treat that result as authoritative for anything dated today.
- **Never pass an explicit `responsibleUser` ID** — it excludes unassigned tasks (i.e., most personal tasks). Leave it unset (default `unassignedOrMe`) or use `responsibleUserFiltering: "all"`.

**Announce at start:** "I'm using the weekly-analysis skill for phase 2 of your weekly review — holistic analysis."

## Phase 0: Clock

Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for today's date. **Do not trust dates from environment metadata, system headers, or injected context** — those are often UTC-stamped and read a day ahead during evening hours west of UTC.

## Phase 1: Gather

1. **Structure** — `get-overview` (no project ID): the area → project tree. Top-level parent projects are GTD *areas*; their sub-projects are GTD *projects*. The **Someday-Maybe** parent and everything under it is the backburner — exclude it from health analysis; it gets its own phase.
2. **Open tasks** — `find-tasks` per area (filter `##AreaName`) or all open tasks; note `labels`, `dueDate`, `addedAt`, `projectId`.
3. **Completed this week** — `find-completed-tasks`, last 7 days anchored on the `user-info` date.

## Phase 2: Project Health

Classify every non-someday project (and any area root holding direct tasks):

| Status | Condition | Response |
|--------|-----------|----------|
| **ACTIVE** | Completions in the last 7 days | Celebrate specific completions; check for blockers |
| **WAITING** | Open tasks exist, all actionable ones are `@waiting` | Verify the wait is still appropriate |
| **INCOMPLETE** | No open non-waiting tasks at all | ⚠️ GTD violation — propose a concrete next action |
| **STALLED** | Open tasks but no completions this week | Recommend one: activate (better next action), defer (move to Someday-Maybe), or abandon |

Also flag projects whose actionable tasks lack an `@next` label — each active project should have exactly one frontline action.

## Phase 3: Area Balance

Per area (top-level parent project): open count, completions this week, aging tasks (added 2+ weeks ago, from `addedAt`). Identify **overloaded** (high count, many aging), **neglected** (no activity), and **balanced** areas. Compare against what the user says matters right now.

## Phase 4: Someday-Maybe Triage

Walk the Someday-Maybe tree (its per-area sub-projects). For each item/project ask: has anything changed that makes this relevant now? Would you do it given a free day? Six months old — time to decide? Then propose one of:

- **Activate** — move out of the Someday-Maybe tree into its real area (with a next action).
- **Keep** — still valid, not now.
- **Remove** — dead; delete.

## Phase 5: Synthesize & Apply (writes only with explicit confirmation)

Present the weekly summary, then apply confirmed changes only: task moves for activations, `update-tasks` for `@next` labels, deletions only item-by-item on explicit confirmation. Never write anything unconfirmed.

## Output Format

```
# Weekly Analysis — [Date]

## PROJECT HEALTH
- ACTIVE ([N]): {project} — [specific win]
- STALLED ([N]): {project} — recommend [activate/defer/abandon]: [why]
- INCOMPLETE ([N]): {project} → suggested next action: [task]
- WAITING ([N]): {project} — waiting on [what]

## AREA BALANCE
- ⚠️ Overloaded: [area] ([N] open, [N] aging)
- ⚠️ Neglected: [area] (no activity for [N] days)
- ✓ Balanced: [areas]

## SOMEDAY-MAYBE TRIAGE
[N] reviewed: [N] activate, [N] keep, [N] remove — with the specific items

## THIS WEEK'S FOCUS
1–3 priorities that follow from the analysis.

## WINS THIS WEEK
- [Completed items worth celebrating]
```

Keep it compact — often read on a phone. **Omit any section that would be empty.**

## Key Principles

- Strategic insights, not task-by-task — hygiene already happened.
- Every active project must end the review with a next action (GTD core).
- Someday-Maybe is an incubator, not a graveyard — be ruthless with dead items.
- End on wins and clear priorities.
- **Display task `content` and project names** — never raw IDs or filter syntax.
