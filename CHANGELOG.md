# Changelog

All notable changes to Tentacles will be documented in this file.

## [1.4.2] - 2026-09-08
### Doctor: relation targets and views
- `doctor` now walks every relation property on each configured database's live schema and classifies the target: `UNREACHABLE` (deleted/trashed/not shared), `OUTSIDE-TEAMSPACE` (target outside `workspace.teamspace_id`), `SHADOW` (target is not the data source the config names for that key — a replaced database). It also checks linked-database views on the landing page and its direct children (`UNREACHABLE (view)`)
- Walk is deduped across the run and capped at 50 distinct target fetches; on hitting the cap it names the databases not walked and how to scope a re-run
- Regeneration explicitly cannot fix these; they need a human edit in Notion
- Motivated by a production fork where a Tasks database was deleted by accident: the config pointed at its replacement, so v1.4's reachability check passed while 38 tickets, two relations, and four views pointed at nothing for ten weeks
- Behavioral update only — no schema or config changes

## [1.4.1] - 2026-09-08
### Template Continuity
- Hosted template landing page reorganized into a two-column layout (Company Ops / Strategy / CRM / Team Resources and Tools) with a *More Databases* toggle for extensions; hub page "Internal Projects" renamed "Internal Project Notes" to stop colliding with the database of the same name
- Onboarding finds the landing page by its real title ("Tentacles Management Layer", falling back to "OS Layer") and walks the column layout; previously it searched for "OS Layer", which only matches the System Map child page
- Labels: the child page "OS Layer — System Map & Reference" renamed "System Map & Reference"; the term "OS Layer" replaced by "Management Layer" throughout the prompt and docs (the config key `os_layer_name` is unchanged)
- Behavioral update only — no schema or config changes

## [1.4] - 2026-09-07
### Hardening & Extensibility
- Mode detection keys on a `"tentacles_config": true` marker instead of a filename glob; templates and examples carry `false`
- Startup config validation: refuses configs with `{PLACEHOLDER}` values, stops on multiple configs, warns once on unknown `system_prompt_version`
- Core Rule 9 *live schema first*: fetch live data source schema once per conversation before select/relation/property writes — config is a hint, live schema is authority
- Core Rule 10: select alters re-declare every existing option (Notion drops unmentioned ones)
- `doctor` / `config doctor`: read-only drift report of config vs live schema, with config regeneration offer
- Database count driven by the config (`databases` + `extensions.databases`); base template still ships 8
- `extensions` config section and `docs/extending.md` for user-added databases (2-hop-to-Tickets rule)
- Onboarding doc-page updates are opt-in via `update docs` (largest onboarding time cost removed from the default path)
- Ops-mode "add a project code" behavior specified (was advertised in docs but not in the prompt)
- Docs reconciled: sample-config to v1.4 shape, registry labels aligned, v1.2 spec marked historical, project-plan moved to `docs/history/`, duplicate agent-patterns collapsed, setup time stated as 15–30 minutes, `docs/process/` build record added
- No schema changes; no template changes

## [1.3] - 2026-03-19
### Granular Dives
- Structured deep work sessions with resumable child databases
- 6 dive templates: Research, Decision Matrix, Project Plan, Content Workshop, Audit, Freeform
- Agent proactively suggests dives on complex tickets
- Sessions persist in Notion and resume across conversations
- New "Dive" type on Tickets

## [1.2.1] - 2026-03-19
### Ticket Scoping Guardrails
- Agent auto-decomposes project-sized requests into sprint-sized tickets
- Scope check runs during ticket creation, onboarding, and migration
- Behavioral update only — no schema or config changes

## [1.2] - 2026-03-19
### Effort Logging, Proactive Alerting, Capacity Planning
- Effort tracking: automatic hour estimation from effort sizes, time logging, variance reporting
- Proactive alerting: 10 configurable health checks across all databases
- Capacity planning: per-person sprint load tracking, assignment guards, velocity
- New Tasks fields: Hours Spent, Hours Estimated
- New Tickets field: Type (Request, Bug, Decision, Alert, Proposal)
- Agent-driven migration from v1.1 configs

## [1.1] - 2026-03-19
### External Source Migration
- Scan and discover databases in any teamspace
- Schema mapping with user approval
- Batch migration with dependency ordering
- Provenance tracking and incremental sync
- Migration during onboarding as fast-track setup path

## [1.0] - 2026-03-19
### Initial Release
- 8-database OS Layer template for Notion
- Claude Project system prompt with onboarding + operations modes
- Agent-driven setup that configures your workspace in ~5 minutes
- Smart ticket creation with suggest-and-confirm workflow
- Config-based versioning system for future upgrades
