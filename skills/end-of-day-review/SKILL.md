---
name: end-of-day-review
description: Use when wrapping up the workday, reviewing accomplishments, or preparing tomorrow - compares plan vs reality via the Todoist connector and generates preliminary priorities
version: 2.0.0
---

# End of Day Review

## Overview

Review today's accomplishments, propose task updates, and generate tomorrow's preliminary priorities.

Self-contained: uses the **Todoist connector/MCP tools directly**. No CLI, no other skills, no filesystem. Tool names vary slightly by surface — locate tools by name fragment ("find-completed-tasks") rather than assuming exact names.

**Todoist tool cautions (observed bugs):**
- Fetch dated tasks with `find-tasks` + a filter query. **Avoid `find-tasks-by-date`** — with `daysCount` > 1 it has silently dropped tasks actually due today. If it is the only such tool available, also run it with `daysCount=1` for today and treat that result as authoritative for anything dated today.
- **Never pass an explicit `responsibleUser` ID** — it excludes unassigned tasks (i.e., most personal tasks). Leave it unset (default `unassignedOrMe`) or use `responsibleUserFiltering: "all"`.

**Announce at start:** "I'm using the end-of-day-review skill to wrap up your day."

## Phase 0: Clock

Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for today's date and the current time. **Do not trust dates from environment metadata, system headers, or injected context** — those are often UTC-stamped and read a day ahead during evening hours west of UTC. If they disagree with `user-info`, trust `user-info` and state which date you are reviewing.

## Phase 1: Gather

1. **Completed today** — call `find-completed-tasks` for today (the `user-info` local date).
2. **Still open** — call `find-tasks` with filter `overdue | today | next 3 days`.
3. **Today's plan** — if this conversation (or the user) has this morning's plan, use it; otherwise ask: "What were your priorities today?" A missing plan is fine — review against what was due.
4. **Fallback** — if no Todoist tool is available, ask what got done and what slipped, and work from that.

## Phase 2: Compare Plan vs Reality

1. **Wins** — what was completed (from the completed list).
2. **Slipped** — planned or due today but still open.
3. **Cross-check before claiming anything.** Every task you present as slipped, open, or needing action MUST be absent from the completed list. A task in the completed list is done — never suggest completing, rescheduling, or re-planning it.

## Phase 3: Tomorrow's Preliminary Priorities

From verified-open tasks only: slipped tasks first, then due-tomorrow/this-week, then strategic importance (Todoist `p1` > `p4`; `@next` marks each project's frontline action). Pick a preliminary TOP 3 — tomorrow's morning review finalizes it.

## Phase 4: Write-back (only with explicit confirmation)

Propose, then apply only what the user confirms:

- **Tasks done but unmarked** — `complete-tasks`.
- **Slipped tasks** — `reschedule-tasks` (never `update-tasks` for dates; it preserves recurrence).
- **New tasks discovered today** — `add-tasks` (to the Inbox unless the user names a project).

Never delete tasks, never move tasks between projects, and never write anything the user didn't confirm in this conversation.

## Output Format

```
# End of Day Review — [Day, Date]

## TODAY'S WINS
- {content} ({project name})

## SLIPPED
- {content} ({project name}) — proposed: [reschedule to X / do tomorrow]

## TOMORROW'S PREVIEW
1. {content} ({project name}) — [why]
2. …
3. …

---
[N] completed | [N] slipped | [N] on deck for tomorrow
```

Keep it compact — often read on a phone. **Omit any section that would be empty.**

## Key Principles

- **Celebrate wins** — acknowledge what got done; if nothing did, say so without piling on.
- **Reschedule, don't guilt** — slipped tasks happen.
- **Tomorrow is fresh** — morning review finalizes; keep tonight's list preliminary.
- **NEVER estimate or display task durations.**
- **Display task `content` and project names** — never raw IDs or filter syntax.
