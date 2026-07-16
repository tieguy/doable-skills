# doable Skills

Claude Code plugin skills for GTD reviews.

## Orchestrating Skills

These are the main entry points - they coordinate the review process:

| Skill | Description | Trigger Phrases |
|-------|-------------|-----------------|
| `morning-review` | Plan your day with time blocks | "morning review", "plan my day" |
| `end-of-day-review` | Wrap up and prep tomorrow | "end of day", "EOD review" |
| `weekly-hygiene` | Task cleanup (phase 1) | "weekly hygiene", "task cleanup" |
| `weekly-analysis` | Strategic analysis (phase 2) | "weekly analysis" |
| `monthly-review` | Strategic patterns and balance | "monthly review" |

## Sub-Skills

Reusable components invoked by orchestrating skills:

| Skill | Purpose |
|-------|---------|
| `deadline-scan` | Analyze urgency tiers |
| `day-planning` | Create time-blocked schedule |
| `end-of-day` | Compare plan vs reality |
| `project-review` | Assess project health |
| `waiting-for-review` | Check delegated items |
| `category-health` | Workload distribution |
| `someday-maybe-review` | Backburner triage |
| `calendar-review` | Tasks from calendar |
| `strategic-patterns` | Monthly pattern analysis |
| `areas-of-focus` | Life area balance |
| `system-health-check` | GTD process audit |

## How It Works

1. User invokes orchestrating skill (e.g., "morning review")
2. Skill fetches data via `doable` CLI
3. Skill invokes sub-skills for analysis
4. Final output presented to user

## Invoking doable

Always use the `doable` command, **never** `python -m cli.main`.



Do NOT attempt to run the CLI by invoking Python directly — dependencies are only available through the installed tool entry point.

## Data Flow

```
User: "Let's do my morning review"
       ↓
morning-review skill
       ↓
doable open --scheduled-today --overdue --due-soon 3 --format json
       ↓
deadline-scan sub-skill (categorize by urgency)
       ↓
day-planning sub-skill (create schedule)
       ↓
Final output to user
```

## Customization

Skills are markdown files with YAML frontmatter. To customize:

1. Copy the skill directory
2. Modify the SKILL.md content
3. Update frontmatter description for new trigger phrases

## Adding New Skills

See the plugin development documentation for creating new skills.
