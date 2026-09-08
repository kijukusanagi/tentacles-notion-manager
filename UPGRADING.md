# Upgrading Tentacles

Tentacles has three layers, each with its own update mechanism:

## 1. System Prompt (agent behavior)
Check the CHANGELOG for the latest version. Compare it to the version comment
at the top of your current system prompt. To upgrade:
1. Copy the new system-prompt.md from this repo
2. Replace your Claude Project's custom instructions with the new version
3. Start a new conversation — the agent will detect the version mismatch
   and offer to run any needed migrations

## 2. Notion Schema (database properties, views)
Schema changes are handled by the agent via migrations. When you update the
system prompt, the agent compares its version against your config file's
`system_prompt_version`. If migrations are available, it will walk you through
them — always with your confirmation before making changes.

You do NOT need to re-duplicate the Notion template for schema updates.

## 3. Config File (your settings)
After running migrations, the agent will generate an updated config file.
Replace the old one in Project Knowledge with the new version.

## Upgrading from v1.0 to v1.1

v1.1 adds External Source Migration. No schema changes.

### What changes
- **Config**: New top-level `migrations` section (`{"sources": []}`)
- **System prompt**: New Migration Mode

### How to upgrade
1. Replace your system prompt with the v1.1 version from `agent/system-prompt.md`
2. Start a new conversation — the agent will detect the version mismatch
3. Say "yes" when it offers to run the v1.0 → v1.1 migration (config-only)
4. Upload the new config to Project Knowledge

## Upgrading from v1.1 to v1.2

v1.2 adds Effort Logging, Proactive Alerting, and Capacity Planning.

### What changes
- **Schema**: Two new number fields on Tasks (Hours Spent, Hours Estimated) and a new Type select on Tickets
- **Config**: Three new top-level sections (effort, alerts, capacity) plus new workflows
- **System prompt**: New sections for Effort Tracking, Proactive Alerting, and Capacity Planning

### How to upgrade
1. Replace your system prompt with the v1.2 version from `agent/system-prompt.md`
2. Start a new conversation — the agent will detect the version mismatch
3. Say "yes" when it offers to run the v1.1 → v1.2 migration
4. The agent will add the new database fields and generate an updated config
5. Upload the new config to Project Knowledge

The migration is non-destructive — no existing data is modified or deleted.

## Upgrading from v1.2 to v1.2.1

v1.2.1 adds ticket scoping guardrails. No schema or config changes — behavioral update only.

### How to upgrade
1. Replace your system prompt with the v1.2.1 version from `agent/system-prompt.md`
2. That's it — no migration needed

## Upgrading from v1.2.1 to v1.3

v1.3 adds Granular Dives — structured deep work sessions with resumable child databases.

### What changes
- **Schema**: "Dive" added to the Type select on Tickets
- **Config**: New top-level `dives` section with template configs and settings
- **System prompt**: New Granular Dives section with 6 templates and session management

### How to upgrade
1. Replace your system prompt with the v1.3 version from `agent/system-prompt.md`
2. Start a new conversation — the agent will detect the version mismatch
3. Say "yes" when it offers to run the v1.2.1 → v1.3 migration
4. The agent will update the Type enum and generate an updated config
5. Upload the new config to Project Knowledge

## Upgrading from v1.3 to v1.4

v1.4 is a hardening and extensibility release. **No schema changes** — the Notion template is untouched.

### What changes
- **Config**: New first key `"tentacles_config": true` (mode-detection marker — replaces the old `*-config-*.json` filename rule) and new top-level `extensions` section (`{"databases": []}`)
- **System prompt**: Startup config validation (refuses placeholder configs, warns on unknown versions), Core Rule 9 *live schema first* and Core Rule 10 *select alters re-declare every option*, a read-only `doctor` command that diffs your config against live Notion schema, database count driven by the config instead of a hardcoded 8, and doc-page updates moved to an opt-in `update docs` command
- **Docs**: `docs/extending.md` (adding your own databases), `docs/process/` (build record), `docs/history/` (superseded planning doc)

### How to upgrade
1. Replace your system prompt with the v1.4 version from `agent/system-prompt.md`
2. Start a new conversation — the agent will detect the v1.3 config and offer the v1.3 → v1.4 migration
3. Say "yes" — it adds the marker and the `extensions` section and regenerates the config (no Notion changes)
4. Upload the new config to your project's Files, replacing the old one, and start a new conversation
5. Say `doctor` — this is the first time the agent compares your config to live schema, and the drift table is worth reading once

If your filename previously matched `*-config-*.json`, it still works — the name no longer matters, only the marker.

**Why the first conversation looks different:** your v1.3 config has no `tentacles_config` marker, which is what v1.4 normally looks for. The v1.4 prompt has a one-time bootstrap rule for exactly this case — a marker-less JSON with `system_prompt_version` and a `databases` map is validated (a template with placeholder IDs is ignored; a real config passes) and then treated as a pre-v1.4 config so the migration can be offered. Once the migrated config with the marker is uploaded, that rule never applies again.

**Forks:** if your fork carries its own version string (e.g. `myfork-1.0`), add it to the known-version list in the Startup: Config Validation section of your fork's prompt; otherwise the agent will warn once per conversation.

## Upgrading from v1.4 to v1.4.1

v1.4.1 aligns the prompt with the reorganized template landing page and retires the term "OS Layer" in favor of "Management Layer". No schema or config changes — behavioral update only.

### How to upgrade
1. Replace your system prompt with the v1.4.1 version from `agent/system-prompt.md`
2. That's it — no migration needed. Existing workspaces keep their current page layout; only fresh duplicates get the new one.

## Upgrading from v1.4.1 to v1.4.2

v1.4.2 extends `doctor` to walk live relation targets and hub-page views. No schema or config changes — behavioral update only.

### How to upgrade
1. Replace your system prompt with the v1.4.2 version from `agent/system-prompt.md`
2. No migration needed
3. Say `doctor` once — the new checks (`UNREACHABLE`, `OUTSIDE-TEAMSPACE`, `SHADOW`, `UNREACHABLE (view)`) may surface drift that existed all along, and none of it can be fixed by regenerating the config; it needs a human edit in Notion

## Version History
See CHANGELOG.md for what changed in each version.
