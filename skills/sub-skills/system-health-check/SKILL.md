---
name: system-health-check
description: Use when auditing GTD system effectiveness - evaluates capture, clarify, organize, review, and engage steps for process health
version: 1.0.0
---

# System Health Check

## Purpose

Evaluate the effectiveness of the GTD system itself.

## The Five GTD Steps

### 1. Capture
- Am I capturing everything that has my attention?
- Is my inbox getting to zero regularly?
- Are items escaping the system?

### 2. Clarify
- Are items clear and actionable?
- Am I clarifying promptly or letting inbox pile up?
- Are next actions truly next actions?

### 3. Organize
- Is the system organized logically?
- Can I find things when I need them?
- Are projects and contexts useful?

### 4. Review
- Am I doing weekly reviews?
- Are reviews effective or just going through motions?
- Is the system current?

### 5. Engage
- Am I working from the system?
- Do I trust the system?
- Am I doing the right things?

## Output

```
### GTD SYSTEM HEALTH

| Step | Status | Notes |
|------|--------|-------|
| Capture | ✓ / ⚠️ / ✗ | [observation] |
| Clarify | ✓ / ⚠️ / ✗ | [observation] |
| Organize | ✓ / ⚠️ / ✗ | [observation] |
| Review | ✓ / ⚠️ / ✗ | [observation] |
| Engage | ✓ / ⚠️ / ✗ | [observation] |

### SYSTEM IMPROVEMENTS NEEDED
- [Specific improvement]
- [Specific improvement]

### WHAT'S WORKING WELL
- [Positive observation]

**IMPORTANT: Always display the task's `title` field, never the `source_url`. The `source_url` is a file path and should never be shown to the user.**
```
