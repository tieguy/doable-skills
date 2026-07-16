---
name: someday-maybe-review
description: Use when triaging backburner items - decides whether to activate, keep, or remove someday/maybe tasks based on relevance and age
version: 1.0.0
---

# Someday/Maybe Review

## Purpose

Review backburner items to decide: activate now, keep for later, or remove.

## Input

Expects tasks from calling skill with:
- Labels containing "someday" or configured backburner label
- Low priority or deferred items

## Process

### Step 1: Identify Backburner Items

Look for:
- Labels: "someday", "maybe", "backburner"
- Projects with status like "paused", "on hold"

### Step 2: Triage Each Item

For each someday/maybe item, decide:

| Decision | Criteria |
|----------|----------|
| **Activate** | Circumstances changed, now relevant |
| **Keep** | Still valid idea, not right time |
| **Remove** | No longer interested, outdated |

### Step 3: Questions to Ask

- Has anything changed that makes this relevant now?
- If I had a free day, would I want to do this?
- Has it been here 6+ months? Time to decide.
- Does it align with current goals/values?

## Output

```
### SOMEDAY/MAYBE TRIAGE (N items reviewed)

**Activate Now:**
- {title} ({project}) - [Why now is the right time]

**Keep on Backburner:**
- {title} ({project}) - Still valid, revisit [when]

**Remove:**
- {title} ({project}) - [Why it's no longer relevant]

**IMPORTANT: Always display the task's `title` field, never the `source_url`.**

### SUMMARY
- [N] items activated
- [N] items kept
- [N] items removed
- [N] items total reviewed
```

## Key Principles

- Someday/maybe is not a graveyard
- Regular review prevents list bloat
- Be ruthless about removing dead items
- 6 months is long enough to decide
