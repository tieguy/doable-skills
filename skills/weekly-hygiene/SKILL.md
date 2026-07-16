---
name: weekly-hygiene
description: Use when doing task cleanup or starting a weekly review - phase 1 that clears the inbox, resolves overdue tasks, and checks waiting items via the Todoist connector
version: 2.0.0
---

# Weekly Hygiene

## Overview

Phase 1 of the two-phase weekly review: task hygiene first, so phase 2 (weekly-analysis) works with accurate data.

Self-contained: uses the **Todoist connector/MCP tools directly**. No CLI, no other skills, no filesystem. Tool names vary slightly by surface — locate tools by name fragment rather than assuming exact names.

**Todoist tool cautions (observed bugs):**
- Fetch dated tasks with `find-tasks` + a filter query. **Avoid `find-tasks-by-date`** — with `daysCount` > 1 it has silently dropped tasks actually due today. If it is the only such tool available, also run it with `daysCount=1` for today and treat that result as authoritative for anything dated today.
- **Never pass an explicit `responsibleUser` ID** — it excludes unassigned tasks (i.e., most personal tasks). Leave it unset (default `unassignedOrMe`) or use `responsibleUserFiltering: "all"`.

**Calendar caution:** call `list_events` with **explicit `startTime`/`endTime` bounds** (e.g. the past 7 days). With no bounds it has returned garbage — far-future recurring all-day events and none of the real ones in your window. Never infer "no events" from an unbounded call.

**Announce at start:** "I'm using the weekly-hygiene skill for phase 1 of your weekly review — task cleanup."

## Phase 0: Clock

Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for today's date and the current time. **Do not trust dates from environment metadata, system headers, or injected context** — those are often UTC-stamped and read a day ahead during evening hours west of UTC. If they disagree with `user-info`, trust `user-info`.

## Phase 1: Gather

1. **Inbox** — `find-tasks` with `projectId: "inbox"`.
2. **Urgent** — `find-tasks` with filter `overdue | today | next 7 days`.
3. **Waiting** — `find-tasks` with label `waiting`.
4. **Completed this week** — `find-completed-tasks` for the last 7 days (anchor "7 days ago" on the `user-info` date).
5. **Calendar (if the Google Calendar tool is available)** — scan the past week's events for meetings that likely produced follow-ups or action items; skip silently if not connected.

## Phase 2: Inbox Clarify (GTD: get to zero)

Walk each inbox item with the user: is it actionable? → propose a project to move it to, a due date, or both. Not actionable? → propose moving to the Someday-Maybe project tree or deleting. Quick (<2 min)? → suggest doing it now.

## Phase 3: Deadline Scan

Tier the urgent tasks: **OVERDUE** (before today), **DUE TODAY**, **DUE THIS WEEK**. "Today" is the `user-info` local date, never a metadata date. For each OVERDUE task, recommend exactly one: complete now, reschedule realistically, or flag as possibly obsolete. Days-overdue counts are computed from the `user-info` date.

## Phase 4: Waiting-For Review

For each `@waiting` task, estimate its age (from `addedAt` or due date) and triage:

| Age | Action |
|-----|--------|
| < 3 days | No follow-up yet |
| 3–7 days | Gentle check-in |
| 7–14 days | Direct follow-up |
| > 14 days | Escalate or decide (unblock, drop, or accept the wait) |

For anything needing follow-up, draft the actual message wording (adjusted for who's being asked — colleague, vendor, approver), not just "follow up".

## Phase 5: Apply Hygiene (only with explicit confirmation)

Compile the full update list, present it, then apply only what the user confirms:

- **Mark done** — `complete-tasks` (tasks finished but unmarked).
- **Reschedule** — `reschedule-tasks` (never `update-tasks` for dates; it preserves recurrence).
- **New tasks** (from calendar follow-ups or discoveries) — `add-tasks` to the right project.
- **Inbox moves** — move confirmed items to their project; someday items into the Someday-Maybe tree.
- **Label changes** — add/remove `waiting` via `update-tasks` as items unblock.
- **Deletions** — only for items the user explicitly confirms deleting, one by one.

## Output Format

```
# Weekly Hygiene — [Date]

## INBOX ([N] items)
- {content} → proposed: [project / date / someday / delete / do now]

## OVERDUE & UPCOMING
- {content} ({project name}) — [N] days overdue → proposed: [action]

## WAITING ITEMS
- {content} ({project name}) — waiting [N] days → "[drafted follow-up message]"

## APPLIED / TO APPLY
[The confirmed update list and its status]

---
Next: run weekly-analysis for phase 2 — holistic analysis with clean data.
```

Keep it compact — often read on a phone. **Omit any section that would be empty.**

## Key Principles

- Hygiene before analysis — never skip to insights over dirty data.
- Draft real follow-up wording, not "follow up with X".
- **NEVER estimate or display task durations.**
- **Display task `content` and project names** — never raw IDs or filter syntax.
