# Upgrading Tentacles

Tentacles has three layers, each with its own update mechanism.

## 1. System Prompt (agent behavior)

Check the [CHANGELOG](CHANGELOG.md) for the latest version. Compare it to the version comment at the top of your current system prompt (`<!-- TENTACLES SYSTEM PROMPT v1.0 -->`). To upgrade:

1. Copy the new `agent/system-prompt.md` from this repo
2. Replace your Claude Project's custom instructions with the new version
3. Start a new conversation — the agent will detect the version mismatch and offer to run any needed migrations

## 2. Notion Schema (database properties, views)

Schema changes are handled by the agent via migrations. When you update the system prompt, the agent compares its version against your config file's `system_prompt_version`. If migrations are available, it will walk you through them — always with your confirmation before making changes.

You do **not** need to re-duplicate the Notion template for schema updates.

## 3. Config File (your settings)

After running migrations, the agent will generate an updated config file. Replace the old one in Project Knowledge with the new version.

## How Versioning Works

The system prompt contains a version comment at the top (`v1.0`) and a Migration Registry section. The config file records which system prompt version generated it (`system_prompt_version` field). On every conversation start, the agent compares these two values:

- **Match** → operates normally
- **Config older** → checks the Migration Registry and offers to run available migrations
- **Config newer** → warns you to update the system prompt from this repo

This means you can always update safely: grab the latest system prompt, paste it in, and the agent handles the rest.

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

---

## Version History

See [CHANGELOG.md](CHANGELOG.md) for what changed in each version.
