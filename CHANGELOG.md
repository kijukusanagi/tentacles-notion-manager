# Changelog

All notable changes to Tentacles will be documented in this file.

## [1.3] - 2026-03-19
### Granular Dives
- Structured deep work sessions with resumable child databases
- 6 dive templates: Research & Analysis, Decision Matrix, Project Plan, Content Workshop, Audit/Review, Freeform
- Session management: start, pause, resume across conversations, close with synthesis
- Proactive dive suggestions during ticket creation and triage
- Integration with effort tracking (log hours per session) and alerting (stale dives surface in health checks)
- Project Plan dives can spawn real tickets and tasks after completion
- New ticket Type enum value: "Dive"
- Config additions: top-level `dives` section with template and suggestion settings
- Migration path from v1.2 → v1.3 included in system prompt migration registry

## [1.2.1] - 2026-03-19
### Ticket Scoping Guardrails
- Added Core Rule: tickets must be sprint-sized (completable by one person in 1-2 weeks)
- Smart Ticket Creation now auto-decomposes project-sized requests into multiple tickets
- Onboarding validates ticket scope from the very first ticket
- Migration mode validates ticket scope during data import
- New agent patterns: "Scope Check" and "Ticket Granularity Audit"
- No schema or config changes — behavioral update only

## [1.2] - 2026-03-19

### Added
- Effort Logging: Hours Spent and Hours Estimated number fields on Tasks, auto-populated from Effort Estimate via configurable hours mapping (XS=1h through XL=16h), prompt on task completion, time rollup queries across tickets/projects/engagements
- Proactive Alerting: 10 configurable health checks — stale tickets, stale P0/P1 tickets, overloaded assignees, orphan tasks, unassigned work, empty engagements, upcoming deadlines, sprint overflow, effort variance — with Critical/Warning/Info severity levels and auto-ticketing for critical alerts
- Capacity Planning: per-user sprint load tracking, assignment guard that warns before overloading someone, team capacity dashboard, velocity tracking across sprints
- Type field on Tickets (Request, Bug, Decision, Alert, Proposal) for categorizing ticket intent
- v1.1 → v1.2 migration entry in Migration Registry — agent auto-detects version mismatch and offers to run schema migration
- New config sections: effort, alerts, capacity (top-level, alongside workspace and databases)
- 12 new agent patterns: log time, time reports, sprint time summary, effort variance check, morning briefing with alerts, health check, targeted alerts, team capacity, smart assignment, sprint planning, velocity report, sprint rebalancing
- `docs/v1.2-release-spec.md` — full v1.2 release specification

### Changed
- Morning briefing now runs all enabled health checks first, presents alert summary at top, then delivers standard status report with capacity snapshot
- Onboarding flow now asks for effort/capacity defaults during Step 2 (Personalize) and creates Hours Spent, Hours Estimated, and Type fields during setup
- Task creation auto-populates Hours Estimated when Effort Estimate is set
- Config template includes effort, alerts, and capacity sections with sensible defaults
- Agent patterns file expanded with Effort Tracking, Proactive Alerting, and Capacity Planning sections

## [1.1] - 2026-03-19

### Added
- External Source Migration support: scan, map, and migrate data from existing Notion teamspaces
- Incremental sync for registered migration sources
- `migrations` section added to config template
- `docs/migration.md` — full migration spec
- `docs/project-plan.md` — open source project plan
- `examples/agent-patterns.md` — practical agent workflow patterns

### Changed
- System prompt updated to v1.1 with Migration mode
- Config template updated to v1.1

## [1.0] - 2026-03-18

### Initial Release
- 8-database OS Layer template for Notion
- Claude Project system prompt with onboarding + operations modes
- Agent-driven setup that configures your workspace in ~5 minutes
- Smart ticket creation with suggest-and-confirm workflow
- Config-based versioning system for future upgrades
- Migration registry framework (empty for v1.0 baseline)
