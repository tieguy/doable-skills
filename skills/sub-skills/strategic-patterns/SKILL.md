---
name: strategic-patterns
description: Use when analyzing completion and deferral patterns over 30 days - surfaces chronic deferrals, easy wins, struggles, and energy insights
version: 1.0.0
---

# Strategic Patterns

## Purpose

Analyze task completion and deferral patterns over the past month to identify strategic insights.

## Input

Expects completed tasks (30 days) and current open tasks from calling skill.

## Process

### Analyze Patterns

**Chronic deferral:** What keeps getting pushed?
- Same tasks appearing week after week
- Indicates resistance or misalignment

**Easy wins:** What flows naturally?
- Tasks completed quickly
- Indicates alignment with energy

**Struggles:** What was hard?
- Tasks that took multiple attempts
- Indicates friction points

**Energy patterns:**
- When did productive work happen?
- What times/contexts worked best?

## Output

```
### STRATEGIC PATTERNS

**Chronically Deferred:**
- [Task/type] - Deferred [N] times
  → Insight: [why this might be happening]

**Easy Wins:**
- [Task/type] - Pattern of quick completion
  → Insight: [what this suggests]

**Struggle Areas:**
- [Task/type] - Took [N] attempts
  → Insight: [what to change]

**Energy Insights:**
- Peak productivity: [when]
- Low energy: [when]

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```
