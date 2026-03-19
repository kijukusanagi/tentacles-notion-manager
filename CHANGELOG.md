# Changelog

All notable changes to Tentacles will be documented in this file.

## [1.2] - 2026-03-19

### Added
- **Effort Logging** — time tracking on tasks via Hours Spent and Hours Estimated properties
- **Proactive Alerting** — 10 configurable health checks with severity levels
- **Capacity Planning** — per-user sprint load tracking with assignment guard
- New `Type` select property on Tickets
- New config sections: `effort`, `alerts`, `capacity`
- `docs/v1.2-release-spec.md` — full v1.2 release specification

### Changed
- System prompt updated to v1.2
- Config template updated to v1.2
- `examples/agent-patterns.md` updated with v1.2 patterns

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
