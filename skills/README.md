# doable skills

GTD review skills, each self-contained and Todoist-connector-native (v2 architecture — no CLI, no sub-skill chaining). Former `sub-skills/` are inlined into their orchestrators.

Built around a **6am–3pm workday that the connector can't see**, so the daily reviews split by time of day:

| Skill | Say | Cadence |
|---|---|---|
| `morning-review` | "set up my day" | daily — intentions + `@agent` dispatch + `@quick`, NOT a schedule |
| `afternoon-block` | "what can I do this evening" | daily (~2:50pm) — 1–2 `@deep`/`@next` items for 3pm→bedtime |
| `end-of-day-review` | "wrap up my day" | daily — reflection |
| `weekly-hygiene` | "weekly review" (phase 1) | weekly |
| `weekly-analysis` | (after hygiene, phase 2) | weekly |
| `monthly-review` | "strategic review" | monthly |
| `daily-next-maintenance` | (scheduled/unattended) | daily |

There is no evening review — `afternoon-block` covers 3pm onward. Labels: `@quick` (margin-sized), `@deep` (focus-block), `@agent` (Louie will expand into a Claude session), `@next` (project frontline action).

Shared conventions: Phase 0 clock from Todoist `user-info` (never environment metadata); someday/maybe = the Someday-Maybe project tree, not a label; writes only with explicit confirmation (`reschedule-tasks` for dates); compact phone-friendly output. Skills that read Google Calendar pass explicit time bounds (unbounded `list_events` returns garbage).
