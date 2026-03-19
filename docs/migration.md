# Tentacles — External Source Migration

## Overview

External Source Migration lets users bring existing Notion data into the Tentacles OS Layer without starting from scratch. The agent scans databases in any teamspace, builds a mapping to the Tentacles schema, and migrates records in user-approved batches — cleanly, with proper project codes, relations, and provenance tracking.

This is not a background sync. It's agent-driven migration: the user asks, the agent scans, proposes a plan, and executes in controlled batches with confirmation at every step.

---

## Design Principles

1. **Read-only on source.** The agent never modifies, deletes, or annotates anything in the source teamspace. All writes go to the Tentacles OS Layer only.

2. **No noise.** Records don't flood into Tentacles unorganized. Every migrated item gets a project code, a proper status mapping, and lands in the right database. Items that can't be cleanly mapped get flagged for the user — never silently dumped.

3. **Batch control.** The user approves every batch before it executes. Batches are ordered by dependency (clients before engagements before tickets before tasks) so relations wire up correctly.

4. **Provenance.** Every migrated record carries a migration tag in its description: `[Migrated from: {source_db_name} | {original_title} | {date}]`. This is the paper trail — you always know where something came from.

5. **Idempotent.** The agent tracks what's been migrated. Running migration again skips already-imported records and only picks up new ones. No duplicates.

---

## How It Works

### Trigger

The user says any of:
- "Migrate my data from [teamspace]"
- "Import my existing [database/teamspace]"
- "Bring in my old [tickets/tasks/projects/clients]"
- "Scan [teamspace] and pull it into Tentacles"
- "sync" / "pull latest" (after an initial migration has been registered)

### Phase 1: Scan & Discover

The agent:

1. Uses `notion-get-teams` to list all teamspaces in the workspace.
2. Asks the user which teamspace to scan (or accepts it from their request).
3. Searches the target teamspace for all databases.
4. For each database found, fetches the schema: property names, types, select/multi-select options, relations.
5. Presents an inventory:

```
I found 5 databases in your "Operations" teamspace:

  1. Project Tracker — 47 items (has: Name, Status, Priority, Assignee, Due Date)
  2. Clients — 12 items (has: Name, Stage, Contact, Deal Value)
  3. Task List — 83 items (has: Task, Status, Project, Assignee)
  4. Meeting Notes — 31 items (has: Title, Date, Attendees)
  5. Resources — 8 items (has: Name, URL, Category)

Databases 1-3 map cleanly to Tentacles. Database 4 (Meeting Notes) doesn't have a direct match — I can bring those in as tickets if you want. Database 5 (Resources) is reference data and probably doesn't need to migrate.

Want me to build a migration plan?
```

### Phase 2: Schema Mapping

For each source database the user wants to migrate, the agent builds a property-level mapping to the target Tentacles database.

#### Automatic Mapping Rules

The agent maps source → target by matching property names and types:

| Source Property Pattern | Target Database | Target Property | Confidence |
|------------------------|-----------------|-----------------|------------|
| Name, Title, Project Name | tickets | Title | High |
| Status (with task-like values) | tickets or tasks | Status | Medium — needs value mapping |
| Priority (any) | tickets or tasks | Priority | Medium — needs value mapping |
| Assignee (person) | tickets or tasks | Assignee | High |
| Due Date, Deadline | tickets or tasks | Due Date | High |
| Client, Company, Account | clients | Name | High |
| Deal Value, Contract Value | clients or engagements | Value / Contract Value | High |
| Description, Notes, Details | tickets | Description | High |
| Sprint, Phase, Milestone | tasks | Sprint | Medium — needs value mapping |

#### Value Mapping for Enums

When a source database uses different status values than Tentacles, the agent proposes a mapping:

```
Your "Project Tracker" uses these statuses:
  Not Started, Active, Paused, Complete, Cancelled

Here's how I'd map them to Tentacles ticket statuses:
  Not Started  →  New
  Active       →  In Progress
  Paused       →  Blocked
  Complete     →  Done
  Cancelled    →  Closed

Look right, or want to adjust any?
```

The same applies for Priority, Sprint, and any other enum field.

#### Unmapped Properties

Properties that don't have a Tentacles equivalent get handled one of three ways:

1. **Append to Description** — Text/rich-text properties that carry useful context get folded into the ticket or task description as a labeled section: `**Original Notes:** {content}`.

2. **Skip** — Properties like "Created By" or "Last Edited" that Tentacles generates automatically are skipped.

3. **Flag for user** — Anything the agent isn't sure about gets presented: "Your source has a 'Department' field with values Engineering, Marketing, Sales. Want me to skip it, add it to the description, or create a new project code for each?"

#### Target Database Selection

Not everything is a ticket. The agent uses heuristics to route source records to the right Tentacles database:

| Source Database Pattern | Primary Target | Secondary Target |
|------------------------|----------------|------------------|
| Project tracker, task list, to-do, backlog | tickets + tasks | — |
| Client list, CRM, contacts, leads | clients | engagements (if deal data exists) |
| Initiative tracker, roadmap, strategy | initiatives | — |
| Sprint board, kanban, task board | tasks | tickets (parent items) |
| OKR tracker, goals, KPIs | okrs | — |
| Partner list, vendors | partnerships | — |

When a source database contains both ticket-level and task-level items (e.g., a project tracker with subtasks), the agent proposes splitting: parent items become tickets, children become tasks linked to those tickets.

### Phase 3: Migration Plan

The agent presents the full plan before executing anything:

```
Here's the migration plan for your "Operations" teamspace:

BATCH 1 — Clients (12 records)
  Source: "Clients" database
  Target: 💼 Client Database
  Mapping: Name → Name, Stage → Status, Deal Value → Value
  Status mapping: Prospect → New Lead, Active → Closed Won, Lost → Closed Lost
  New project codes needed: ACME, GLBX, PERS (will add to Tickets enum)

BATCH 2 — Engagements (8 records)
  Source: "Clients" database (records with active contracts)
  Target: 📁 Active Engagements
  Mapping: Name + " Engagement" → Engagement Name, Deal Value → Contract Value
  Relations: Will link to Client records created in Batch 1

BATCH 3 — Tickets (47 records)
  Source: "Project Tracker" database
  Target: 🎫 Tickets
  Mapping: Name → Title, Status → Status, Priority → Priority, Assignee → Assignee
  Project codes: Will assign based on client linkage or default to {PREFIX}-OPS
  ⚠️ 8 items have no description — I'll create them with title only and flag as needs-triage
  Relations: Will link to Clients/Engagements from Batches 1-2

BATCH 4 — Tasks (83 records)
  Source: "Task List" database
  Target: ✅ Tasks
  Mapping: Task → Task Name, Status → Status, Project → Source Ticket (lookup)
  Relations: Will link to Tickets from Batch 3 where project names match
  ⚠️ 15 tasks don't match any ticket — I'll create placeholder tickets for them

SKIPPING:
  - Meeting Notes (31 records) — no direct Tentacles equivalent
  - Resources (8 records) — reference data, not operational

Total: 150 records across 4 batches
Estimated time: ~10 minutes with confirmations

Ready to start with Batch 1?
```

### Phase 4: Batch Execution

For each batch:

1. **Preview** — Show the first 3-5 records as they'll appear in Tentacles, with all mapped properties filled in. This lets the user spot mapping problems before the full batch runs.

2. **Confirm** — User approves, adjusts, or skips.

3. **Execute** — Agent creates records via Notion MCP, setting:
   - All mapped properties
   - `Source = "Agent"` on tickets
   - Migration provenance in Description
   - Relations to previously-created records (using Notion page URLs from earlier batches)

4. **Report** — Summary of what was created:
   ```
   Batch 1 complete: 12 client records created.
     - 3 new project codes added: ACME, GLBX, PERS
     - All statuses mapped successfully
     - Ready for Batch 2?
   ```

5. **Pause point** — User can stop between batches, adjust mappings, or skip ahead. The agent tracks batch progress in the migration state so it can resume later.

### Phase 5: Post-Migration

After all batches complete:

1. **Summary ticket** — Agent creates a ticket (Project Code: `{PREFIX}-OPS`, Source: Agent) documenting the full migration: what was imported, source teamspace, record counts, any items that were flagged or skipped.

2. **Config update** — Agent adds the migration source to the config file under a new `migrations` section so future syncs know what's been imported.

3. **Offer follow-up** — "Want me to triage the migrated tickets? I can review them and suggest priority adjustments now that they're in the system."

---

## Incremental Sync (Re-Migration)

After an initial migration is registered, the user can say "sync" or "pull latest from [source]" to pick up new records.

### How It Works

1. Agent reads the migration config to find registered sources.
2. Queries the source database for records created or modified after the last sync timestamp.
3. For new records: runs them through the same mapping and presents a batch for approval.
4. For modified records: presents a diff showing what changed in the source and what the corresponding Tentacles record currently looks like. User decides per-record whether to update.
5. Updates the last sync timestamp in the migration config.

### What Syncs

- **New records** in the source database → proposed as new Tentacles records
- **Status changes** in source records → proposed as status updates on existing Tentacles records
- **Title/name changes** → proposed as updates
- **New properties** added to source schema → flagged for the user to decide on mapping

### What Doesn't Sync

- **Deletions** — If a record is deleted from the source, the Tentacles record stays. The agent never deletes data.
- **Tentacles-native fields** — Project Code, relations to other OS Layer databases, Serialized ID, and other Tentacles-specific fields are never overwritten by source data.
- **Conflicts** — If a record was modified in both the source and Tentacles since the last sync, the agent flags it and asks the user which version to keep.

---

## Config Schema Additions

The config file gets a new top-level `migrations` section:

```json
{
  "migrations": {
    "sources": [
      {
        "source_id": "migration_001",
        "source_teamspace_id": "abc123-...",
        "source_teamspace_name": "Old Operations",
        "registered_at": "2026-03-19",
        "last_synced_at": "2026-03-19T15:30:00Z",
        "databases": [
          {
            "source_database_id": "db-xxx",
            "source_database_name": "Project Tracker",
            "target_database": "tickets",
            "record_count": 47,
            "property_mapping": {
              "Name": {"target": "Title", "type": "direct"},
              "Status": {
                "target": "Status",
                "type": "enum_map",
                "values": {
                  "Not Started": "New",
                  "Active": "In Progress",
                  "Paused": "Blocked",
                  "Complete": "Done",
                  "Cancelled": "Closed"
                }
              },
              "Priority": {
                "target": "Priority",
                "type": "enum_map",
                "values": {
                  "Critical": "P0",
                  "High": "P1",
                  "Medium": "P2",
                  "Low": "P3"
                }
              },
              "Assignee": {"target": "Assignee", "type": "direct"},
              "Due Date": {"target": "Due Date", "type": "direct"},
              "Notes": {"target": "Description", "type": "append"}
            },
            "migrated_record_ids": ["page-id-1", "page-id-2", "..."]
          },
          {
            "source_database_id": "db-yyy",
            "source_database_name": "Clients",
            "target_database": "clients",
            "record_count": 12,
            "property_mapping": {
              "Name": {"target": "Name", "type": "direct"},
              "Stage": {
                "target": "Status",
                "type": "enum_map",
                "values": {
                  "Prospect": "New Lead",
                  "Active": "Closed Won",
                  "Lost": "Closed Lost"
                }
              },
              "Deal Value": {"target": "Value", "type": "direct"}
            },
            "migrated_record_ids": ["page-id-a", "page-id-b", "..."],
            "generated_project_codes": ["ACME", "GLBX", "PERS"]
          }
        ]
      }
    ]
  }
}
```

### Key Config Fields

| Field | Purpose |
|-------|---------|
| `source_id` | Unique migration identifier (for referencing in conversation) |
| `source_teamspace_id` | The teamspace being read from — agent uses this to scope queries |
| `last_synced_at` | Timestamp of last successful sync — used for incremental queries |
| `property_mapping` | The approved field mapping — stored so re-syncs use the same rules |
| `migrated_record_ids` | Notion page IDs of all source records that have been imported — prevents duplicates |
| `generated_project_codes` | Any project codes created during migration — tracked for rollback awareness |

---

## System Prompt Additions

### Mode Detection Update

Add to the existing mode detection logic:

```
- **If the user says "migrate", "import", "bring in", "pull from", or "sync":**
  Enter MIGRATION MODE. This works in both onboarding and operations — during
  onboarding it runs after Step 2 (Personalize) and before Step 3 (Learn by Doing).
  In operations, it runs as a standalone workflow with its own ticket.
```

### New Section: Migration Mode

```
# ═══════════════════════════════════════════
# MIGRATION MODE
# ═══════════════════════════════════════════

Migration brings existing Notion data into the OS Layer. It can run during
onboarding (as an accelerated setup path) or anytime in operations mode.

## Safety Rules

1. **READ-ONLY on source.** Never create, update, or delete anything in the
   source teamspace. All MCP write operations target only the Tentacles
   teamspace databases.

2. **Batch confirmation required.** Never execute a batch without explicit
   user approval. Present the plan, get a "yes" or adjustments, then execute.

3. **No silent failures.** If a record can't be mapped cleanly, flag it.
   Never skip records without telling the user.

4. **Provenance always.** Every migrated record includes in its Description:
   [Migrated from: {source_db_name} | {original_title} | {date}]

5. **No duplicates.** Track migrated record IDs in the config. Before creating
   any record, check if its source page ID is already in migrated_record_ids.

## Migration During Onboarding

If the user mentions existing data during onboarding, offer migration as a
fast-track option after Step 2:

"I see you have existing databases in [teamspace]. Want me to scan them and
bring your data into Tentacles? This replaces the 'learn by doing' step —
your real data becomes your first tickets and tasks."

If yes: run Scan → Map → Plan → Execute. The first migrated ticket and task
serve as the "learn by doing" example. Then proceed to Step 4 (Generate Config)
with the migration sources included.

If no: proceed with standard onboarding Step 3.

## Migration in Operations Mode

When triggered in operations mode:

1. Create a migration ticket first:
   Title: "Data Migration from {source_teamspace_name}"
   Project Code: {PREFIX}-OPS
   Priority: P1
   Source: Agent
   Description: Documents the migration scope and plan

2. Run Scan → Map → Plan → Execute as described in the spec.

3. After completion, update the migration ticket to Done with a summary comment
   documenting: records imported, batches executed, any items skipped or flagged.

4. Regenerate the config file with the new migrations section and ask the
   user to re-upload it to Project Knowledge.

## Incremental Sync

When the user says "sync" or "pull latest":

1. Load migration sources from config.
2. If multiple sources exist, ask which one (or "all").
3. Query source databases for records created/modified after last_synced_at.
4. Present new and changed records for approval.
5. Execute approved changes.
6. Update last_synced_at in config.
7. Add a comment to the migration ticket with sync results.

## Scan Workflow Detail

### Discovering Databases

Use notion-search scoped to the source teamspace to find all databases.
For each database:
  - Fetch its schema via notion-fetch (using data source URL)
  - Count records via notion-query-database-view
  - Catalog all properties with their types

### Building the Property Map

For each source property, attempt automatic mapping:

1. Exact name match to a Tentacles property → map directly
2. Semantic match (e.g., "Task" → "Task Name", "Company" → "Name") → map with note
3. Same type, plausible match → suggest to user
4. No match → present options: append to description, skip, or ask user

For select/multi-select properties, compare values against Tentacles enums:
- Exact matches → map directly
- Close matches (case differences, abbreviations) → suggest mapping
- No match → present the source values and ask user for mapping

### Batch Ordering

Always migrate in dependency order:

1. **Clients** — no dependencies
2. **Partnerships** — may reference clients
3. **Engagements** — references clients
4. **Initiatives** — may reference clients, engagements
5. **Internal Projects** — may reference initiatives, engagements
6. **OKRs** — may reference engagements
7. **Tickets** — references all of the above
8. **Tasks** — references tickets

This ensures relations can be wired up as records are created. When creating
a ticket that should link to a client imported in Batch 1, the agent uses
the Notion page URL of the previously-created client record.

### Handling Parent-Child Structures

If a source database has parent-child relationships (subtasks, sub-items):

1. Identify parent records → these become **tickets**
2. Identify child records → these become **tasks**
3. Create tickets first (in the tickets batch)
4. Create tasks with Source Ticket relation pointing to the parent ticket
5. If nesting is deeper than 2 levels, flatten: level 1 = ticket,
   levels 2+ = tasks with Parent Task / Subtask self-relations

### Creating Project Codes from Source Data

If source records are organized by project, client, or category:

1. Extract unique grouping values (e.g., project names, client names)
2. Suggest a project code for each (following naming conventions)
3. Present to user for approval
4. Add approved codes to the Tickets database enum via MCP
5. Track generated codes in config under generated_project_codes
```

---

## Agent Patterns (New Section)

Add to the agent-patterns.md file:

### Migrate Existing Data

> "Scan my Operations teamspace and bring everything into Tentacles."

The agent discovers all databases in the target teamspace, builds a schema mapping to the OS Layer, and presents a batch migration plan. You review and approve each batch. Records come in with proper project codes, status mappings, and provenance tags.

**When to use:** First setup when you have existing Notion data, or when you've been managing work in a separate teamspace and want to consolidate.

### Incremental Sync

> "Sync — anything new in my old Operations teamspace?"

The agent checks registered migration sources for records created or modified since the last sync. New items get proposed as a batch; changed items show a diff for your review.

**When to use:** Transition period when some team members are still using the old system, or when external data flows into a source teamspace.

### Selective Migration

> "Just bring in the clients and active projects from my Sales teamspace — skip everything else."

You can scope migration to specific databases or even filter by status. The agent adjusts the scan and plan accordingly.

### Re-Map and Re-Sync

> "The status mapping for the Project Tracker import isn't right — 'Paused' should map to 'On Hold' on engagements, not 'Blocked' on tickets."

You can adjust mappings after the initial migration. The agent updates the config and offers to fix already-migrated records.

---

## Edge Cases & Error Handling

### Source Database Has No Clear Tentacles Equivalent
Present to user with options: "This database doesn't map to any of the 8 OS Layer databases. I can bring items in as tickets (generic intake), skip it entirely, or you can tell me what it should map to."

### Source Record Missing Required Fields
If a source record is missing a Tentacles required field (e.g., no title), the agent flags it in the batch preview: "3 records have no title — I'll use their first property value as the title. OK?"

### Relation Targets Don't Exist Yet
If a source record references another record that hasn't been migrated (e.g., a ticket references a client not in the migration), the agent flags it: "This ticket references 'Acme Corp' but that client isn't in the migration. Want me to create the client record too, or leave the relation empty?"

### Very Large Databases (100+ Records)
For databases with many records, the agent proposes sub-batches: "Your Project Tracker has 200 records. I'll migrate them in groups of 25 so you can spot-check as we go. Each group takes about 2 minutes."

### Duplicate Detection
Before creating any record, the agent checks if a record with the same title already exists in the target Tentacles database. If found: "A ticket titled 'Website Redesign' already exists. Skip this import, or create it with a '(migrated)' suffix?"

### Schema Changes in Source
On incremental sync, if the source database has new properties that weren't in the original mapping, the agent flags them: "The Project Tracker now has a 'Department' property that wasn't there before. Want me to add it to the mapping?"

---

## Implementation Priority

### v1.1 — Core Migration
- Scan & discover databases in any teamspace
- Schema mapping with user approval
- Batch execution with dependency ordering
- Provenance tracking in descriptions
- Migration config section
- Migration during onboarding (fast-track path)
- Migration in operations mode (with source ticket)
- System prompt additions

### v1.2 — Incremental Sync
- Last-synced timestamp tracking
- New record detection and batch proposal
- Modified record diff and selective update
- Schema change detection

### v1.3 — Advanced
- Sub-batch support for large databases
- Duplicate detection and merge suggestions
- Mapping adjustment and retroactive fix
- Multi-source sync dashboard (summary of all registered sources)
