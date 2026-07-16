---
name: day-planning
description: Use when creating today's schedule after deadline analysis - builds time-blocked plan with TOP 3 priorities and realistic task fitting
version: 1.0.0
---

# Day Planning

## Purpose

Create a realistic, time-blocked schedule for today based on available time windows and prioritized tasks. This is a sub-skill called by orchestrating skills.

## Input

Expects from calling skill:
- Priority-sorted tasks from deadline-scan
- User's availability windows (from config or ask user)
- Current time

## Process

### Step 1: Establish Available Time

1. **What time is it now?** Only schedule future slots.
2. **What are the availability windows?**
   - Check config for weekday/weekend patterns
   - Or ask user: "What time windows are available today?"
3. **Account for existing commitments** if calendar data available.

### Step 2: Apply 3-Step Priority Algorithm

1. **Urgency**: Overdue → Due today → Due this week → Other
2. **Feasibility**: Fits in available windows? Needs focus time?
3. **Strategic value**: Unblocks others? Aligns with goals?

### Step 3: Create Schedule

**Scheduling rules:**
- Leave buffer slots between tasks
- Complex tasks in morning focus time
- Quick tasks can fill gaps
- Don't overschedule — leave room for unexpected

**Task fitting:**
- Max 3-5 tasks for realistic completion
- **NEVER estimate or display task durations** — you have no way of knowing how long tasks take. Only assign time slots, not durations.

### Step 4: Handle Overflow

Tasks that don't fit today:
- Note as "deferred to tomorrow" if due this week
- Flag for rescheduling if overdue
- Accept that not everything fits

## Output

Return to calling skill:

```
### TODAY'S TOP 3 PRIORITIES
1. {title} ({project}) - [Why it's priority]
2. {title} ({project}) - [Why it's priority]
3. {title} ({project}) - [Why it's priority]

### TIME-BLOCKED SCHEDULE
| Time | Task |
|------|------|
| 9:00 | {title} |
| 10:00 | {title} |
| 10:45 | Buffer/email |
| 11:00 | {title} |

### DEFERRED TO TOMORROW
- {title} ({project}) - Reason for deferral

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**

**IMPORTANT: NEVER estimate or display task durations (e.g. "15 min", "1 hr"). You do not know how long tasks take. Only assign time slots.**
```

## Key Principles

- **Be realistic**: Better to complete 3 tasks than plan 10
- **Protect focus time**: Don't fragment morning with small tasks
- **Build in slack**: Things take longer than expected
- **No rituals**: Don't suggest "shutdown routines" - just the plan
