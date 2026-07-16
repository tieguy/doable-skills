---
name: waiting-for-review
description: Use when checking delegated or blocked items - assesses age, suggests follow-up wording, and prioritizes by urgency
version: 1.0.0
---

# Waiting-For Review

## Purpose

Review all waiting-for items to ensure timely follow-ups and prevent items from being forgotten.

## Input

Expects tasks from calling skill. Look for:
- `labels` array containing "waiting"
- Task titles starting with "waiting:", "blocked:"
- `due_date` to calculate age

## Process

### Step 1: Identify Waiting Items

Scan tasks for:
- Labels include "waiting" (or configured waiting label)
- Title patterns: "waiting for", "blocked by"

### Step 2: Assess Age and Priority

For each waiting item:

| Age | Urgency | Action |
|-----|---------|--------|
| < 3 days | Low | No follow-up yet |
| 3-7 days | Medium | Gentle check-in |
| 7-14 days | High | Direct follow-up |
| > 14 days | Critical | Escalate or decide |

### Step 3: Generate Follow-Up Language

Don't just say "follow up" - provide actual wording:

**For team members:**
> "Hey [name], checking in on [item]. Is there anything blocking this? Happy to help if needed."

**For vendors:**
> "Following up on [item] from [date]. Could you provide a status update?"

**For approvals:**
> "Circling back on [item]. Let me know if you need any additional information to move forward."

## Output

```
### WAITING-FOR ITEMS (N items)

**Needs Follow-up Now:**
- {title} ({project}) - Waiting [N] days for [who/what]
  → Suggested message: "[actual follow-up wording]"

**Check-in Soon:**
- {title} ({project}) - Waiting [N] days

**Recently Started:**
- {title} ({project}) - Waiting [N] days (no action needed yet)

**IMPORTANT: Always display the task's `title` field, never the `source_url`.**
```

## Key Principles

- Provide actual follow-up wording, not just "follow up"
- Consider relationship context (team vs vendor vs boss)
- Age matters: older = more urgent
