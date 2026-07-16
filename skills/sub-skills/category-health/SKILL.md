---
name: category-health
description: Use when analyzing workload distribution - identifies overloaded, neglected, and balanced categories and maps to life areas
version: 1.0.0
---

# Category Health

## Purpose

Assess workload distribution to identify neglected areas and overloaded categories.

## Input

Expects tasks grouped by project from calling skill.

## Process

### Step 1: Calculate Metrics Per Category

For each project/category:
- **Total tasks**: Open task count
- **Aging tasks**: Created 2+ weeks ago
- **Recent activity**: Tasks created/completed last 7 days

### Step 2: Identify Patterns

**Overloaded:** (High task count, many aging)
- Consider: defer some tasks, break into smaller chunks
- Sign: feeling overwhelmed in this area

**Neglected:** (Zero recent activity, all aging)
- Consider: schedule dedicated time, or move to backburner
- Sign: important area not getting attention

**Balanced:** (Reasonable count, mix of new/in-progress)
- Maintain current approach

### Step 3: Map to Life Areas

Consider GTD areas of responsibility:
- Work / Career
- Health / Fitness
- Family / Relationships
- Finances
- Personal Development
- Home / Environment

Which areas are getting attention? Which are neglected?

## Output

```
### CATEGORY HEALTH

**Overloaded** (needs attention)
| Category | Open | Aging | Status |
|----------|------|-------|--------|
| [Name] | [N] | [N] | ⚠️ Consider deferring some |

**Neglected** (no recent activity)
| Category | Open | Last Activity |
|----------|------|---------------|
| [Name] | [N] | [N] days ago |

**Balanced** (healthy)
| Category | Open | Recent |
|----------|------|--------|
| [Name] | [N] | [N] |

### LIFE AREA BALANCE
- ✓ Work/Career: Active
- ⚠️ Health/Fitness: Neglected
- ✓ Family: Balanced

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```

## Key Principles

- Balance across life areas matters
- Overload leads to burnout
- Neglect becomes crisis eventually
