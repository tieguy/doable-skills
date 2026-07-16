---
name: monthly-review
description: Use when doing a monthly or strategic review - analyzes completion patterns, life area balance, and GTD system health over the past 30 days
version: 1.0.0
---

# Monthly Review

## Overview

Strategic-level reflection covering patterns, areas of focus, and GTD system health.

**Announce at start:** "I'm using the monthly-review skill for your monthly strategic review."


## Quick Reference

| Phase | Focus | Output |
|-------|-------|--------|
| 1 | Data Gathering | Month's data |
| 2 | Strategic Patterns | What's recurring? |
| 3 | Areas of Focus | Life balance |
| 4 | System Health | GTD effectiveness |
| 5 | Monthly Summary | Strategic priorities |

## When to Use

- End of month reflection
- Feeling misaligned or overwhelmed
- After major project completion
- Quarterly if not doing dedicated quarterly reviews

## The Process

### Phase 1: Data Gathering

```bash
doable open --format json
doable completed --days 30 --format json
doable projects --format json
```

### Phase 2: Strategic Patterns

Analyze the last 30 days:
- What tasks were chronically deferred?
- What got done easily vs with struggle?
- Any recurring themes in completions?
- Energy/motivation patterns?

### Phase 3: Areas of Focus

**INVOKE SUB-SKILL:** doable:areas-of-focus

Map activity to life areas:
- Work / Career
- Health / Fitness
- Family / Relationships
- Finances
- Personal Development
- Home / Environment

Which got attention? Which were neglected?

### Phase 4: System Health Check

**INVOKE SUB-SKILL:** doable:system-health-check

Evaluate GTD process:
- **Capture**: Am I capturing everything?
- **Clarify**: Are items clear and actionable?
- **Organize**: Is the system organized?
- **Review**: Am I reviewing regularly?
- **Engage**: Am I doing the right things?

### Phase 5: Monthly Summary

Synthesize into strategic priorities for next month.

## Output Format

```
# Monthly Review - [Month Year]

## STRATEGIC PATTERNS
**Chronically deferred:** [patterns]
**Easy wins:** [what flowed]
**Struggles:** [what was hard]

## AREAS OF FOCUS BALANCE
| Area | Status | Notes |
|------|--------|-------|
| Work | ✓ Active | [notes] |
| Health | ⚠️ Neglected | [notes] |
| Family | ✓ Balanced | [notes] |

## GTD SYSTEM HEALTH
- Capture: [status]
- Clarify: [status]
- Organize: [status]
- Review: [status]
- Engage: [status]

## NEXT MONTH'S PRIORITIES
1. [Strategic priority]
2. [Strategic priority]
3. [Strategic priority]

## MONTH'S WINS
- [Celebrate accomplishments]

---
*Monthly review complete. Carry these priorities into your weekly reviews.*

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```

## Error Handling

Always use the `doable` command, **never** `python -m cli.main`. 

## Key Differences from Weekly

| Aspect | Weekly | Monthly |
|--------|--------|---------|
| Focus | Tactical | Strategic |
| Horizon | Days/Week | Weeks/Month |
| Output | Tasks | Priorities |
