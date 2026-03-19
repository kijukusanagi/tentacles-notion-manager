# Changelog

All notable changes to Tentacles will be documented in this file.

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
