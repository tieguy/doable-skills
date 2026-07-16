---
name: deadline-scan
description: Use when tasks need urgency analysis - groups by overdue, due today, due this week, and recommends actions per tier
version: 1.0.0
---

# Deadline Scan

## Purpose

Analyze tasks with due dates to identify urgency tiers and recommend actions. This is a sub-skill called by orchestrating skills like morning-review.

## Input

Expects task data already gathered by calling skill. Tasks should have:
- `due_date`: ISO date or null
- `title`, `project`, `source`, `source_url`

The calling skill passes task data; this skill does not fetch data.

## Process

### Step 1: Categorize by Urgency

Group tasks into tiers:

| Tier | Condition | Label |
|------|-----------|-------|
| 1 | `due_date < today` | **OVERDUE** |
| 2 | `due_date == today` | **DUE TODAY** |
| 3 | `due_date` within 7 days | **DUE THIS WEEK** |
| 4 | `due_date` within 14 days | **DUE NEXT WEEK** |

### Step 2: Assess Each Deadline

For tasks in tiers 1-3, evaluate:

1. **Is the deadline realistic?**
   - Can it still be met?
   - Does it need rescheduling?

2. **What preparation is needed?**
   - Single session or multiple steps?
   - Dependencies?

3. **Priority type:**
   - Hard deadline (external) vs soft (personal)?
   - Consequences of missing?

### Step 3: Generate Recommendations

| Tier | Recommended Action |
|------|-------------------|
| OVERDUE | Complete urgently, reschedule realistically, or delete if obsolete |
| DUE TODAY | Schedule specific time block |
| DUE THIS WEEK | Identify when to start, prep steps |
| DUE NEXT WEEK | Note to schedule next week |

## Output

Return to calling skill:
- Tasks grouped by urgency tier
- Days overdue/until due annotations
- Recommended action per task
- Summary counts per tier

**Format** (always use the task's `title` field as display text, never `source_url`):
```
### OVERDUE (N tasks)
- {title} ({project}) - X days overdue - [Recommended action]

### DUE TODAY (N tasks)
- {title} ({project}) - [Recommended action]

### DUE THIS WEEK (N tasks)
- {title} ({project}) - Due [Day] - [Recommended action]

Summary: N overdue | N due today | N due this week
```
