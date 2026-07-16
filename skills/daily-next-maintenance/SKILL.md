---
name: daily-next-maintenance
description: Use daily to enforce the @next invariant via the Todoist connector - exactly one next action per active project, promote successors when tasks complete, flag stalled projects
version: 2.0.0
---

# Daily Next Maintenance

## Overview

Maintain the `@next` invariant across Todoist: **at most one open, non-waiting task per active project carries `@next`.** This skill computes the label changes needed, applies them within strict bounds, and reports projects that need human attention.

Self-contained: uses the **Todoist connector/MCP tools directly**. No CLI, no other skills, no filesystem. Tool names vary slightly by surface — locate tools by name fragment rather than assuming exact names.

**Todoist tool cautions (observed bugs):**
- Fetch dated tasks with `find-tasks` + a filter query. **Avoid `find-tasks-by-date`** — with `daysCount` > 1 it has silently dropped tasks actually due today. If it is the only such tool available, also run it with `daysCount=1` for today and treat that result as authoritative for anything dated today.
- **Never pass an explicit `responsibleUser` ID** — it excludes unassigned tasks (i.e., most personal tasks). Leave it unset (default `unassignedOrMe`) or use `responsibleUserFiltering: "all"`.

**Announce at start:** "I'm running daily next-maintenance to keep your next-action labels current."

## Phase 0: Clock

Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for today's date. **Do not trust dates from environment metadata, system headers, or injected context** — often UTC-stamped and a day ahead in evening hours west of UTC.

## Phase 1: Gather

1. `get-overview` (no project ID) — the project tree. **Scope: every project node outside the Someday-Maybe tree, at any nesting depth, treated independently** — an area parent counts as its own project for its *direct* tasks only, separate from its children. **The Inbox and the Someday-Maybe tree are entirely out of scope** (Inbox items are pre-triage; they get clarified in weekly-hygiene, not labeled here).
2. For each in-scope project, its open tasks with `labels`, `dueDate`, `content`, `parentId` (`find-tasks` per project, or one bulk fetch grouped by `projectId`).

## Phase 2: Plan (deterministic rules — follow exactly)

For each in-scope project, let **actionable** = its open **top-level** tasks (no `parentId` — subtasks are never `@next` candidates) whose labels do NOT include `waiting`:

| Case | Action |
|------|--------|
| No actionable tasks | Flag project as **zero-action** (stalled) — no label change |
| Actionable, none has `next` | **Set** `next` on the *primary* task |
| Exactly one has `next` | OK — no change |
| More than one has `next` | Keep the *primary* among the labeled ones; **clear** `next` from the rest |

**Primary task selection (apply mechanically, no judgment):**
1. Earliest `dueDate` wins; tasks with **no due date sort last**.
2. Ties broken alphabetically by `content`, case-insensitive (for stability).

Do not override the selection because a different task "seems more important" — the rule is the rule; the user re-points `@next` manually or in reviews if they disagree. Project descriptions (Purpose / Desired Outcome) may be quoted to *explain* a selection, never to change it.

## Phase 3: Apply (bounded writes)

- Allowed writes: **adding or removing the `next` label via `update-tasks`. Nothing else.** Never complete, reschedule, move, create, or delete anything in this skill.
- **Cap: 15 label changes per run.** If the plan is larger, apply the 15 most urgent — due-dated changes first (soonest first), then undated ones alphabetically by `content`, case-insensitive — and list the remainder in the report. A plan that big usually means something structural changed and deserves human eyes.
- Interactive session: show the plan, then apply on confirmation. Unattended/scheduled run: apply without confirmation (the allowlist and cap are the guardrails) and produce the report.

## Phase 4: Report

```
# Next Maintenance — [Date]

## APPLIED
- +@next: {content} ({project name}) — due {dueDate}
- −@next: {content} ({project name}) — duplicate cleared

## STALLED (no actionable tasks)
- {project name} — [all waiting / empty] → needs a next action or a defer decision

## OK
[N] projects already compliant
```

**Omit any section that would be empty.** If nothing changed and nothing is stalled: one line — "All [N] projects compliant."

## Key Principles

- The invariant is self-healing: a missed run is corrected by the next one. Prefer doing less over improvising.
- Stalled projects are a *report*, not something to fix silently — inventing next actions is review work, done with the user.
- **Display task `content` and project names** — never raw IDs or filter syntax.
