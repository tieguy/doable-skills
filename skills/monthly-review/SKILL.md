---
name: monthly-review
description: Use when doing a monthly or strategic review - analyzes 30-day completion patterns, life-area balance, and GTD system health via the Todoist connector
version: 2.0.0
---

# Monthly Review

## Overview

Strategic-level reflection: recurring patterns, life-area balance, and the health of the GTD system itself. Tactical task cleanup belongs to the weekly review, not here.

Self-contained: uses the **Todoist connector/MCP tools directly**. No CLI, no other skills, no filesystem. Tool names vary slightly by surface — locate tools by name fragment rather than assuming exact names.

**Todoist tool cautions (observed bugs):**
- Fetch dated tasks with `find-tasks` + a filter query. **Avoid `find-tasks-by-date`** — with `daysCount` > 1 it has silently dropped tasks actually due today. If it is the only such tool available, also run it with `daysCount=1` for today and treat that result as authoritative for anything dated today.
- **Never pass an explicit `responsibleUser` ID** — it excludes unassigned tasks (i.e., most personal tasks). Leave it unset (default `unassignedOrMe`) or use `responsibleUserFiltering: "all"`.

**Announce at start:** "I'm using the monthly-review skill for your monthly strategic review."

## When to Use

End of month; feeling misaligned or overwhelmed; after a major project completes.

## Phase 0: Clock

Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for today's date. **Do not trust dates from environment metadata, system headers, or injected context** — often UTC-stamped and a day ahead in evening hours west of UTC. The 30-day window is anchored on this date.

## Phase 1: Gather

1. **Structure** — `get-overview`: areas (top-level parents), projects, the Someday-Maybe tree.
2. **Completed, 30 days** — `find-completed-tasks` for the last 30 days (anchored on the `user-info` date).
3. **Open tasks** — `find-tasks` across areas; note `addedAt`, `dueDate`, `labels`, `priority`.
4. **Stats (optional)** — if `get-productivity-stats` is available, pull it for completion trends; skip silently otherwise.

## Phase 2: Strategic Patterns

Across the 30-day window:

- **Chronically deferred** — open tasks added long ago and repeatedly rescheduled (old `addedAt`, still due-dated). Name the resistance: wrong task, wrong project, or wrong goal?
- **Easy wins** — what flowed (clusters of quick completions, by project or type).
- **Struggles** — what took repeated pushes.
- **Cadence** — bursts vs. steady progress; which weeks were dead and why (travel, load, season).

## Phase 3: Areas of Focus (GTD Horizon 2)

Your areas are the top-level parent projects in Todoist. For each: tasks completed (30d), open count, aging count. Mark ✓ active / ⚠️ neglected / ⚠️ overloaded. Compare the *distribution* against what the user says their priorities are — misalignment between stated priorities and actual activity is the key monthly finding.

## Phase 4: GTD System Health

Evaluate with objective signals where possible:

| Step | Signal |
|------|--------|
| **Capture** | Inbox count now; are things living outside the system? |
| **Clarify** | Vague task names ("handle X", "look into Y") in the open list |
| **Organize** | Tasks in the wrong projects; projects missing from areas |
| **Review** | Were weekly reviews actually run this month? Stalled-project count |
| **Engage** | `@next` coverage: what fraction of active projects have exactly one `@next`? |

## Phase 5: Synthesize

Distill into 1–3 strategic priorities for next month, and list the month's wins. Any resulting task changes follow the standard rule: propose, apply only what the user explicitly confirms (`reschedule-tasks` for dates, `update-tasks` for labels, never unconfirmed writes, never deletions without item-by-item confirmation).

## Output Format

```
# Monthly Review — [Month Year]

## STRATEGIC PATTERNS
Chronically deferred: … | Easy wins: … | Struggles: …

## AREAS OF FOCUS
| Area | Done (30d) | Open | Status |
|------|-----------|------|--------|

## GTD SYSTEM HEALTH
Capture / Clarify / Organize / Review / Engage — ✓ ⚠️ ✗ with one-line notes

## NEXT MONTH'S PRIORITIES
1–3 strategic priorities.

## MONTH'S WINS
- [Accomplishments worth naming]
```

Keep it compact — often read on a phone. **Omit any section that would be empty.**

## Key Principles

- Strategic, not tactical: priorities out, not task lists.
- Judge balance against *stated* priorities, not an ideal of even distribution.
- **Display task `content` and project names** — never raw IDs or filter syntax.
