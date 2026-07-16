---
name: calendar-review
description: Use when reviewing calendar for missed action items - identifies tasks from meetings, follow-ups, and recurring commitments
version: 1.0.0
---

# Calendar Review

## Purpose

Review calendar for the past week to identify tasks that should be created from meetings, events, and commitments.

## Input

Calendar data from the past 7 days (if available).

## Process

1. Review each meeting/event
2. Identify action items mentioned
3. Check for follow-ups needed
4. Note recurring commitments

## Output

```
### CALENDAR-BASED TASKS

**From Meetings:**
- [Meeting name] → Task: [action item]

**Follow-ups:**
- [Event] → Follow up with [person] about [topic]

**Recurring:**
- [Weekly meeting] → Prep task needed?

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```

## Note

If calendar integration is not available, ask user to manually review their calendar and report action items.
