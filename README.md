# doable-skills

GTD review skills for Claude — daily planning, end-of-day wrap-up, weekly and monthly reviews — targeting [Todoist](https://todoist.com) as the task store via the Todoist connector/MCP.

Packaged as a Claude plugin: works in Claude Code, claude.ai, Claude Desktop, and Cowork.

## Skills

| Skill | Say | What it does |
|---|---|---|
| `morning-review` | "plan my day" | Time-blocked plan with TOP 3 priorities from overdue/today/due-soon tasks |
| `end-of-day-review` | "wrap up my day" | Plan vs. reality, preliminary tomorrow priorities |
| `weekly-hygiene` | "weekly review" | Phase 1: inbox clarify, overdue decisions, waiting follow-ups |
| `weekly-analysis` | (after hygiene) | Phase 2: project health, area balance, Someday-Maybe triage |
| `monthly-review` | "strategic review" | 30-day patterns, life-area balance, GTD system health |
| `daily-next-maintenance` | (scheduled) | Enforces the `@next` invariant: ≤1 next action per active project |

All skills are self-contained and connector-native (v2): clock from Todoist `user-info`, writes only with explicit confirmation, phone-compact output.

## Setup

1. Connect the Todoist connector (and optionally Google Calendar) to your Claude account.
2. Install this repo as a plugin (from the plugin directory / GitHub URL), or upload individual skill folders as zips under Settings → Capabilities → Skills.
3. Structure Todoist GTD-style: areas as parent projects, projects as sub-projects, `@next`/`@waiting` labels, a Someday-Maybe project tree.

## License

CC0 1.0 (public domain dedication)
