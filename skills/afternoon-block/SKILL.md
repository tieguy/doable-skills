---
name: afternoon-block
description: Use in the late afternoon (workday ending) or asking "what can I do this evening" - picks one or two real focus items for the 3pm-onward free block from the Todoist connector
version: 1.0.0
---

# Afternoon Block

## Overview

Fires as the 6am–3pm workday ends (~2:50pm). Plans the **real personal free block — roughly 3pm to bedtime** — where actual project and focus work happens. Unlike morning-review (fast intentions + dispatch, no schedule), this picks **one or two** genuine `@deep` / project-`@next` items sized to the evening Louie actually has, accounting for evening commitments and post-workday energy. There is no separate evening review: this one covers the whole rest of the day.

Self-contained: uses the **Todoist connector/MCP tools directly** (and Google Calendar if connected). No CLI, no other skills, no filesystem. Tool names vary slightly by surface — locate tools by name fragment rather than assuming exact names.

**Todoist tool cautions (observed bugs):**
- Fetch dated tasks with `find-tasks` + a filter query. **Avoid `find-tasks-by-date`** — with `daysCount` > 1 it has silently dropped tasks actually due today. If it is the only such tool available, also run it with `daysCount=1` for today and treat that result as authoritative for anything dated today.
- **Never pass an explicit `responsibleUser` ID** — it excludes unassigned tasks (i.e., most personal tasks). Leave it unset (default `unassignedOrMe`) or use `responsibleUserFiltering: "all"`.

**Calendar caution:** call `list_events` with **explicit `startTime`/`endTime` bounds** for the window you care about (e.g. now → end of today). With no bounds it has returned garbage — a run of far-future recurring all-day events and none of today's real ones. Never infer "no commitments" from an unbounded call.

**Announce at start:** "I'm using the afternoon-block skill to plan your evening."

## Phase 0: Clock

Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for now and today's date. **Do not trust dates from environment metadata, system headers, or injected context** — often UTC-stamped and a day ahead in evening hours west of UTC. Only plan time *after* `currentLocalTime`. If it's well before ~3pm (skill invoked early), that's fine — plan "from now until bedtime" and say so; the logic is unchanged.

## Phase 1: Gather

1. **Deep work** — `find-tasks` with label `deep` (focus-block work).
2. **Frontline actions** — `find-tasks` with label `next` (each active project's next action), excluding the Someday-Maybe tree.
3. **Due/overdue still open** — `find-tasks` with filter `overdue | today`, in case something dated still needs finishing tonight.
4. **Evening calendar** — if the Google Calendar tool is available, list the rest of today's events to find commitments that eat the evening (dinner plans, a call, kid bedtime). Skip silently if not connected.
5. **Project names** — `get-overview` (no project ID) to map `projectId` → names.

1. **How much real free time is there?** From `currentLocalTime` to a **default bedtime of ~10pm** (unless the user says otherwise), minus evening calendar commitments (Phase 1.4). Then discount for honest post-workday energy: after a 6am–3pm day, assume only **~1–2 hours of genuine focus** are realistically left, even if the clock shows more. Size the picks to *that*, not to the raw clock gap.
2. **FOCUS slots go to substantial work only** — pick ONE or TWO from `@deep`, else project `@next`. One item that advances an active project beats several hopeful ones. **Fallback:** if `@deep` is empty, use `@next`; if both are empty, say so and offer to help choose from open project tasks.
3. **Due-today items do NOT consume focus slots.** Anything still due today that's quick/admin goes in a separate "close out today" line — knock-it-out work, not the evening's focus. (Only a genuinely substantial due-today task earns a focus slot.)
4. **Name what "done for tonight" looks like** for each focus item — a concrete stopping point, not the whole project.

## Phase 3: Write-back (only with explicit confirmation)

Propose, then apply only what the user confirms:

- **Reschedules** — `reschedule-tasks` (never `update-tasks` for dates).
- **Completions** the user reports — `complete-tasks`.
- **`@deep` / `@next` labels** — offer via `update-tasks` when useful.

Never delete tasks, never move tasks between projects, never write anything unconfirmed.

## Output Format

```
# Tonight — [Day, Date]

## EVENING COMMITMENTS
- [time] {event}

## CLOSE OUT TODAY (quick)
- {content} ({project name})   ← due today, admin/quick — knock out, not focus

## FOCUS (pick 1–2)
1. {content} ({project name}) — done-for-tonight looks like: [concrete stop]
2. …

## IF THERE'S MORE TIME
- {content} ({project name})
```

Keep it compact — read on a phone. **Omit any section that would be empty.** No durations, no minute-by-minute grid — just the one or two things that matter and where to stop.

## Key Principles

- **One or two real items**, sized to the evening that's actually left — not an ambitious list.
- **This is the evening planner** — there is no later review; end-of-day is reflection, not planning.
- **Respect post-workday energy** — the clock overstates how much focus is left after a 9-hour day.
- **Display task `content` and project names** — never raw IDs or filter syntax.
