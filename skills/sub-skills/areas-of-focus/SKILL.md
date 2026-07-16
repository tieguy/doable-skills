---
name: areas-of-focus
description: Use when evaluating life area balance - maps task activity to GTD Horizon 2 areas (work, health, family, finances, personal, home)
version: 1.0.0
---

# Areas of Focus

## Purpose

Evaluate balance across GTD Horizon 2 - Areas of Responsibility.

## Input

Tasks and completions from calling skill, mapped to life areas.

## Life Areas (GTD Horizon 2)

- **Work / Career:** Job, professional development
- **Health / Fitness:** Exercise, medical, wellness
- **Family / Relationships:** Partner, kids, friends
- **Finances:** Money, investments, budgeting
- **Personal Development:** Learning, hobbies, growth
- **Home / Environment:** House, car, possessions

## Process

1. Map tasks to areas
2. Count activity per area
3. Identify gaps and imbalances
4. Compare to stated priorities

## Output

```
### AREAS OF FOCUS BALANCE

| Area | Tasks | Completions | Status |
|------|-------|-------------|--------|
| Work | [N] | [N] | ✓ Active |
| Health | [N] | [N] | ⚠️ Neglected |
| Family | [N] | [N] | ✓ Balanced |
| Finances | [N] | [N] | - Minimal |
| Personal | [N] | [N] | ⚠️ Neglected |
| Home | [N] | [N] | ✓ Balanced |

### IMBALANCE ALERTS
- [Area] has had no activity in [N] weeks
- [Area] is overloaded relative to others

### RECOMMENDATIONS
- Schedule time for [neglected area]
- Consider delegating some [overloaded area]

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```
