---
name: morning-review
description: Use when starting the day or asking "what should I work on today" - a fast personal intentions + agent-dispatch review (NOT a time-blocked schedule) built from the Todoist connector
version: 3.0.0
---

# Morning Review

## Overview

A fast start-of-day pass for **personal life around a 6am–3pm workday**. The workday itself is invisible (it lives in a work calendar the connector can't see), so this review does NOT build a time-blocked schedule. It sets a few intentions, flags any hard-time personal commitments, surfaces quick wins for the day's margins, and lists tasks ripe to hand to an agent session. Bigger project work is planned separately by the **afternoon-block** review (~2:50pm).

Self-contained: uses the **Todoist connector/MCP tools directly** (and Google Calendar if connected). No CLI, no other skills, no filesystem. Tool names vary slightly by surface — locate tools by name fragment ("find-tasks", "list events") rather than assuming exact names.

**Todoist tool cautions (observed bugs):**
- Fetch dated tasks with `find-tasks` + a filter query. **Avoid `find-tasks-by-date`** — with `daysCount` > 1 it has silently dropped tasks actually due today. If it is the only such tool available, also run it with `daysCount=1` for today and treat that result as authoritative for anything dated today.
- **Never pass an explicit `responsibleUser` ID** — it excludes unassigned tasks (i.e., most personal tasks). Leave it unset (default `unassignedOrMe`) or use `responsibleUserFiltering: "all"`.

**Calendar caution:** call `list_events` with **explicit `startTime`/`endTime` bounds** (e.g. today 00:00 → 23:59). With no bounds it has returned garbage — far-future recurring all-day events and none of today's real ones. Never infer "nothing scheduled" from an unbounded call.

**Announce at start:** "I'm using the morning-review skill to set up your day."

## Phase 0: Clock

Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for today's date. **Do not trust dates from environment metadata, system headers, or injected context** — often UTC-stamped and a day ahead in evening hours west of UTC.

## Phase 1: Gather

1. **Today's tasks** — `find-tasks` with filter `overdue | today`. Note `content`, `dueDate`, `priority`, `projectId`, `labels`.
2. **Quick wins** — `find-tasks` with label `quick` (things that fit a lunch break or a micro-gap).
3. **Agent candidates** — `find-tasks` with label `agent` (tasks Louie plans to expand into a Claude session himself — this review surfaces them, it does NOT launch anything).
4. **Personal calendar for today** — if the Google Calendar tool is available, list today's events. **This is the point of the calendar step:** the work calendar is invisible, so anything here is likely a *personal* commitment with a hard time — a doctor's appointment, a school pickup, a call. Flag those prominently. An empty/missing list means nothing personal scheduled, not an error. Skip silently if not connected.
5. **Project names** — `get-overview` (no project ID) to map `projectId` → names.

## Phase 2: Assemble the day

Not a schedule — a short orientation:

- **Hard commitments today** — personal calendar events with fixed times (from Phase 1.4). These are the only true time anchors.
- **Intentions (aim for ~3)** — small, human, doable around the workday: a relational touch (a short conversation with Krissa or Asa), a medical/admin errand, a `@quick` win. Favor things that fit the margins (before 6am, lunch, right after 3pm) over anything needing a focus block — focus work is the afternoon review's job.
- **Agent queue** — the `@agent` tasks, listed as candidates to open a session for. For each, a one-line note on what a session would tackle, so Louie can pick one to expand into a prompt (ideally teed up before the workday so it runs while he's at work).
- **Overdue** — anything genuinely overdue gets a one-line flag with a proposed action (do today / reschedule), but don't try to schedule it here.

## Phase 3: Write-back (only with explicit confirmation)

Propose, then apply only what the user confirms:

- **Reschedules** — `reschedule-tasks` (never `update-tasks` for dates; it preserves recurrence).
- **Completions** the user reports — `complete-tasks`.
- **`@next` / `@quick` / `@agent` labels** — offer to add/adjust via `update-tasks` when a task obviously wants one and lacks it.

Never delete tasks, never move tasks between projects, never write anything unconfirmed.

## Output Format

```
# Morning — [Day, Date]

## HARD TODAY (personal calendar)
- [time] {event}

## INTENTIONS
- {content} ({project name})   ← ~3, relational / quick / admin

## AGENT QUEUE (expand one into a session?)
- {content} ({project name}) — [what a session would do]

## QUICK WINS
- {content} ({project name})

## OVERDUE
- {content} ({project name}) → [do today / reschedule]
```

Keep it very compact — read on a phone in under a minute. **Omit any section that would be empty.** No time grid, no durations.

## Key Principles

- **This is not a schedule.** Intentions and dispatch, not time blocks. The afternoon-block review plans the real free hours.
- **The workday is invisible** — never plan into 6am–3pm; treat calendar hits as personal commitments.
- **Margins, not focus blocks** — save `@deep`/project work for the afternoon review.
- **Surface `@agent`, don't launch** — Louie opens sessions himself.
- **Display task `content` and project names** — never raw IDs or filter syntax.
