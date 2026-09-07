# Extending Tentacles with your own databases

The base template ships 8 core databases. Since v1.4 the agent operates on whatever the config lists — the core set under `databases`, plus anything you register under `extensions.databases`. This page covers how to add one, what the agent will and won't do with it, and the rule that keeps repo and template in sync.

---

## The two-artifact rule

Tentacles has two artifacts that must agree:

| Artifact | Lives in | Who changes it |
|---|---|---|
| **The Notion template** — databases, properties, relations, views | Your Notion workspace (duplicated from the hosted template) | You, in the Notion UI (or the agent, via `update-data-source`, with your confirmation) |
| **The config** — IDs, enums, relations, conventions the agent reads | Your Claude Project's Files | The agent generates it; you edit `extensions` by hand |

Adding a database means touching both. Creating it in Notion without registering it in the config makes it invisible to the agent. Registering it in the config without creating it in Notion makes `doctor` report it `UNREACHABLE` and the agent refuse to write to it. Neither is a bug — that's the check working.

The upstream repo (`agent/config-template.json`, the hosted template) stays schema-neutral: **no extension database ships in the base template.** Extensions are yours.

---

## How to add a database

1. **Build it in Notion**, inside your Tentacles teamspace, under the OS Layer page. Give it a title property, a `Status` select if you want it in health checks, and — this is the important part — a relation to **Tickets** or to a core database that relates to Tickets (see the 2-hop rule below).

2. **Get its IDs.** Open the database as a full page; the URL contains the database ID. Ask the agent: *"What's the data source ID for the {name} database?"* — it can fetch it. Or run `doctor` after step 3 and read the IDs from the drift table.

3. **Add an entry to `extensions.databases`** in your config. Copy `extensions._example_entry` from `agent/config-template.json` as a starting shape:

   ```json
   {
     "key": "meetings",
     "database_id": "…",
     "data_source": "…",
     "access": "read_write",
     "role": "Meeting log. One row per meeting; action items become tickets.",
     "relations_to_tickets": "Related Meetings",
     "title_property": "Name",
     "required_fields": ["Name", "Date"],
     "optional_fields": ["Attendees", "Notes", "Related Tickets"],
     "enums": { "Status": ["Scheduled", "Held", "Cancelled"] },
     "relations": { "Related Tickets": "tickets" },
     "other_properties": { "Date": "date", "Attendees": "person (multiple)" }
   }
   ```

   Field notes:
   - `key` — short snake_case handle the agent uses to refer to it. Must not collide with a core key (`tickets`, `tasks`, `engagements`, `initiatives`, `internal_projects`, `clients`, `partnerships`, `okrs`).
   - `role` — free text. The agent reads this to decide when the database is relevant.
   - `relations_to_tickets` — the name of the relation property **on Tickets** that points at this database, or `null` if Tickets has no direct relation to it.
   - `access` — `read_write`, `read_only`, or `write_only`. The agent honors it.
   - `relations` — same free-text convention as core entries: the target database key is the first token; anything in parentheses is descriptive.

4. **Re-upload the config**, start a new conversation, and say **`doctor`**. The drift table will show the new entry as reachable and list any property names that don't match live. Fix and repeat until clean.

5. **Optional: extend the Tickets relation.** If you set `relations_to_tickets`, that property must actually exist on Tickets. Create it in the Notion UI (a relation from Tickets to your new database) — or ask the agent to add it; it will fetch the live Tickets schema first (Core Rule 9).

---

## The 2-hop rule

Tickets are the intake layer. Everything the agent does — cross-linking, ticket-driven queries, "show me everything about X" — walks relations outward from Tickets, and it only walks two hops. An extension database is visible to that layer if **either**:

- `relations_to_tickets` is set (1 hop), **or**
- its `relations` map names a core database that itself relates to Tickets — e.g. a relation to `clients` (Tickets → Clients → your DB = 2 hops).

If neither holds, the agent will still read and update the database directly when you ask, but it will tell you once that it can't cross-link it from tickets. That's usually a sign you want to add the relation.

---

## What the agent will do with an extension database

- Read and write rows, honoring `access`, using live schema (Core Rule 9) and re-declaring select options on alters (Core Rule 10).
- Include it in `doctor` — reachability, property drift, enum drift, relation targets.
- Include it in generic health checks (stale items, unassigned work, upcoming deadlines) **if** the entry declares the properties those checks need (`Status` enum, an assignee/person property, a date property). Checks whose inputs aren't declared are skipped for that database, silently.
- Cross-link from tickets if the 2-hop rule holds.
- Offer it as an explicit target when you migrate data in — never auto-routed.

## What the agent won't do

- **Add it to the config for you.** Registration is a deliberate, human step. The agent can tell you the IDs; you paste the entry.
- **Create alert tickets from extension-only checks** unless the entry declares a `Status` enum.
- **Discover it during onboarding.** Onboarding finds the 8 core databases by name and nothing else. Extensions are added after the config exists.
- **Ship it upstream.** If an extension turns out to be broadly useful, it becomes a candidate for an optional module in a future version — see the roadmap note below — but the base template stays at 8.

---

## Removing an extension

Delete its entry from `extensions.databases`, re-upload the config, start a new conversation. The Notion database is untouched — the agent never deletes data. If Tickets has a relation property pointing at it, remove that in Notion by hand or leave it; `doctor` will flag it as config-says-but-live-lacks only if the config still mentions it.

---

## Roadmap note

Two extensions that production forks have built and found useful — a **Meetings** log and a **Suggested Tickets** queue — are candidates for v1.5 as optional modules (opt-in entries with a documented schema, still not in the base template). They are not part of v1.4.
