# Changelog

All notable changes to Tentacles will be documented in this file.

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
