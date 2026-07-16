---
name: morning-review
description: Use when starting the day, planning today's work, or asking "what should I work on" - fetches tasks from the Todoist connector and creates a time-blocked execution plan with TOP 3 priorities
version: 2.0.0
---

# Morning Review

## Overview

Create today's execution plan from live Todoist data, accounting for overnight additions and yesterday's incomplete work.

Self-contained: uses the **Todoist connector/MCP tools directly** (and Google Calendar if connected). No CLI, no other skills, no filesystem.

**Announce at start:** "I'm using the morning-review skill to plan your day."

## Quick Reference

| Phase | Action | Output |
|-------|--------|--------|
| 1 | Gather (Todoist + calendar) | Today's task + commitment context |
| 2 | Deadline scan | Urgency-tiered tasks with recommendations |
| 3 | Day planning | TOP 3 + time-blocked schedule |
| 4 | Write-back (confirmed only) | Reschedules, `@next` labels |

## Phase 1: Gather

Tool names vary slightly by surface (prefixes, hyphens vs underscores) — locate tools by name fragment ("find-tasks", "list events") rather than assuming exact names.

0. **Clock — establish "today" and "now" first.** Call the Todoist `user-info` tool and use its `currentLocalTime` and `timezone` as the ONLY source of truth for today's date and the current time. **Do not trust dates from environment metadata, system headers, or injected context** — those are often UTC-stamped and read a day ahead during evening hours west of UTC. If metadata disagrees with `user-info`, trust `user-info` and state plainly which date you are planning (e.g. "Planning Wednesday, July 15 — your local evening"). If `user-info` is unavailable, a live localized shell clock (`date`) is the fallback; environment metadata is never the tiebreaker.
1. **Tasks** — call the Todoist `find-tasks` tool with the filter query:
   ```
   overdue | today | next 3 days
   ```
   If that query errors, try `overdue | today | 3 days`. Note each task's `id`, `content`, `description`, `dueDate`, `recurring`, `priority`, `projectId`, `labels`.
2. **Project names** — call `get-overview` once (no project ID) to map `projectId` → project name; it returns the hierarchy directly. Always display project *names*, never IDs.
3. **Calendar (if the Google Calendar tool is available)** — list today's events to learn which time slots are already committed. An empty or missing events list means a free day, not an error. Skip silently if not connected.
4. **Fallback** — if no Todoist tool is available at all, say so and ask the user to paste their task list; continue with Phases 2–3 on that.

## Phase 2: Deadline Scan

Group tasks by urgency tier:

| Tier | Condition |
|------|-----------|
| **OVERDUE** | `dueDate` before today |
| **DUE TODAY** | `dueDate` is today |
| **DUE SOON** | `dueDate` within the next 3 days |

"Today" here is the `user-info` local date from Phase 1, never a metadata date. (Todoist evaluates filter words like `overdue`/`today` server-side in the user's own timezone, so if your tiering disagrees with what the filter returned, the filter is right and your date arithmetic is wrong.)

For OVERDUE and DUE TODAY tasks, assess: is the deadline still realistic? Hard (external) or soft (personal) deadline? Recommend one action each: complete today, reschedule realistically, or flag as possibly obsolete.

Tasks overdue by exactly one day are usually **slipped from yesterday** — call them out as such.

These tiers are internal analysis, not output sections. They map to the output as follows: overdue-by-one-day → "Slipped from Yesterday"; all OVERDUE and DUE TODAY tasks → candidates for TOP 3 and the schedule; DUE SOON tasks that don't make the schedule → "Also on Radar".

## Phase 3: Day Planning

1. **Establish available time.** "Now" is `user-info`'s `currentLocalTime` from Phase 1 — only schedule future slots. Subtract calendar commitments from Phase 1. If availability is unclear, ask: "What time windows are available today?"
2. **Prioritize** (in order): urgency tier → Todoist priority (`p1` highest … `p4` default) → feasibility in today's windows → strategic value (unblocks other work, aligns with stated goals). Tasks with the `@next` label are each project's designated frontline action — favor them when otherwise equal.
3. **Schedule.** Complex work in morning focus time; quick tasks fill gaps; leave buffer between blocks; max 3–5 tasks. Accept that not everything fits.
4. **Overflow.** Due-soon tasks that don't fit: "deferred to tomorrow". Overdue tasks that don't fit: propose a reschedule in Phase 4.

## Phase 4: Write-back (only with explicit confirmation)

Propose, then apply only what the user confirms:

- **Date changes** — use the Todoist `reschedule-tasks` tool, **never** `update-tasks`, for moving due dates (it preserves recurrence and time-of-day).
- **Completions** the user reports — `complete-tasks`.
- **`@next` adoption** — if a project surfaced today has no `@next` task, offer to label its obvious frontline action (`update-tasks`, add label `next`). At most one `@next` per project; skip anything in the Someday-Maybe project tree.

Never delete tasks, never move tasks between projects, and never write anything the user didn't confirm in this conversation.

## Output Format

```
# Morning Review — [Day, Date]

## Slipped from Yesterday
[Tasks due yesterday and still open, if any]

## TOP 3 PRIORITIES FOR TODAY
1. {content} ({project name}) — [why]
2. …
3. …

## TIME-BLOCKED SCHEDULE
| Time | Task |
|------|------|
| [start time] | {content} |

## ALSO ON RADAR (due within 3 days)
- {content} ({project name}) — due {dueDate}

---
[N] tasks scanned | [N] overdue | [N] due today
```

Keep the plan compact — it is often read on a phone. Prefer the short table over prose. **Omit any section that would be empty** (including "Slipped from Yesterday" and "Also on Radar") rather than printing a header with "none".

## Key Principles

- **Focus on TODAY only** — tomorrow gets planned tomorrow (or tonight).
- **Be realistic** — 3–5 tasks max for completion.
- **NEVER estimate or display task durations** ("15 min", "1 hr") — you do not know how long tasks take. Assign start times only.
- **No rituals** — don't suggest "shutdown routines" or extra ceremonies; just the plan.
- **Display task `content` and project names** — never raw IDs, URLs, or filter syntax.
