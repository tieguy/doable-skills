---
name: daily-next-maintenance
description: Use daily to enforce the @next invariant - maintain exactly one next action per project, promote successors when tasks complete, and flag stalled projects with no next actions
version: 1.0.0
---

# Daily Next Maintenance

## Overview

Maintain the `@next` label invariant across Todoist projects. This skill runs the automated next-action enforcement engine, optionally refines chosen actions using stored project context, and surfaces projects in need of attention (zero next-actions or stalled).

**Announce at start:** "I'm running daily next-maintenance to keep your next-action labels current."

## Quick Reference

| Phase | Action | Output |
|-------|--------|--------|
| 1 | Enforce Invariant | Plan of label changes |
| 2 | Context Refinement | Next-action justifications |
| 3 | Stalled Project Report | Zero-action projects flagged |

## The Process

### Phase 1: Enforce @next Invariant

Run the enforcement engine in dry-run mode first to review the plan, then apply changes.

**Step 1a: Preview the plan**

```bash
doable enforce-next --dry-run --format json
```

Parse the JSON output to understand:
- `set_next`: tasks being labeled as next (project + title)
- `clear_next`: tasks losing the next label (duplicates being cleared)
- `zero_action`: projects with no next action (stalled)
- `ok`: projects already compliant

**Step 1b: Apply the plan**

```bash
doable enforce-next --format json
```

This applies all label changes:
- Sets exactly one `@next` per active project
- Clears duplicate `@next` labels
- Promotes successors when the current next action completes

Announce: "Next-action labels updated. Promoting successors and cleaning duplicates."

### Phase 2: Context Refinement (Optional)

For projects flagged with changes or zero-action status, optionally fetch stored project context to explain the choice to the user.

**Step 2a: List all context**

```bash
doable context --format json
```

**Step 2b: For each project with context, show it**

For projects that appeared in `set_next` or `zero_action`, if context exists for that project ID:

```bash
doable context --project-id <project-id> --format table
```

This retrieves:
- **Purpose:** Why the project exists
- **Desired Outcome:** What success looks like
- **Notes:** Relevant context or blockers

Announce: "Showing project context for next-action choices:"

Sample output:

```
Project: Area Name (project/area)
Purpose: [Context purpose]
Desired Outcome: [What we're aiming for]
Notes: [Any relevant context]
---
Next Action: [task title]
Rationale: [Why this task unblocks progress]
```

### Phase 3: Stalled Project Report

Projects with zero next-actions indicate a blocker or decision needed. Surface them explicitly.

**Identify stalled projects** from Phase 1's `zero_action` list.

For each, check:
1. Is the project complete? (Mark done if so)
2. Is there a blocker? (Check context from Phase 2)
3. Is there work to do? (If yes, add a next action or ask user)

**Announce:** "Projects without next actions (may be blocked or completed):"

Report stalled projects with table format:

```
| Project | Status | Action |
|---------|--------|--------|
| [name] | Blocked / Complete / Review | [Recommendation] |
```

Sample:

```
| Areas/Work | Blocked | Waiting for stakeholder feedback — marked @waiting |
| Goals/Personal Growth | Complete | All tasks done; consider archiving |
| Goals/Systems | Review | Has tasks but no next action; needs review |
```

## Output Format

Present the maintenance summary:

```
# Daily Next Maintenance - [Date]

## Labels Updated
- [count] tasks labeled @next
- [count] duplicate @next labels cleared
- [count] projects now compliant

## Next Actions

### Promoted
[If any tasks were promoted after completion]
- [project]: [task title]

### New
[If any new next actions were set]
- [project]: [task title]

## Projects Needing Attention

### Stalled (Zero Next Actions)
[If any projects have no next action]
- [project] — [Status: Blocked/Complete/Review] — [Action needed]

### Context Snapshots (For Projects with Context)
[If user has stored context for any changed projects]

#### Project: [Area/Project Name]
- **Purpose:** [purpose]
- **Status:** [outcome]
- **Notes:** [notes]
- **Next Action:** [task] — [Rationale]

---
*[Count] total projects compliant | [Count] stalled | [Count] with context*
```

## Error Handling

Always use the `doable` command, **never** `python -m cli.main`. 

If `doable enforce-next` fails:
1. Check if Todoist adapter is enabled in config.yaml
2. Check if TODOIST_API_KEY is set
3. Check if active projects exist in Todoist

If `doable context` returns empty, context may not be migrated yet — proceed with enforcement only.

## Key Principles

- **One @next per active project** — the invariant that makes reviews work
- **Automated, not manual** — prevents degradation from upkeep fatigue
- **Stalled projects are visible** — zero actions = decision needed
- **Context-aware** — when available, show why the next action was chosen
- **Lightweight data flow** — no task dumps, just enforcement + context

## Next Step

After this skill completes, stalled projects can be handled via interactive triage or context review:

> "For any stalled projects, review the context and either add a next action, mark the project complete, or move it to someday."
