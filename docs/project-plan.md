# Tentacles — Open Source Project Plan

## What it is

An open-source, agent-ready operational backbone built in Notion. 8 interconnected databases that track everything from strategic initiatives to individual tasks. Ships with a Claude Project system prompt that onboards new users by building out their actual system — learn by doing — then seamlessly transitions into a production operations agent.

## Distribution: GitHub repo

### Repo structure

```
tentacles/
├── README.md                           # Overview, screenshots, quickstart
├── LICENSE
├── SETUP.md                            # Detailed setup guide (3 steps)
├── UPGRADING.md                        # How existing users get new versions
├── CHANGELOG.md                        # Version history and migration notes
│
├── notion-template/
│   └── TEMPLATE_LINK.md                # Link to public Notion template to duplicate
│
├── agent/
│   ├── system-prompt.md                # The system prompt (paste into Claude Project)
│   ├── config-template.json            # Agent config with placeholder values
│   └── system-prompt-template.json     # System prompt JSON with placeholder values
│
├── docs/
│   ├── architecture.md                 # How the 8 databases connect
│   ├── project-codes.md                # How project codes work
│   ├── workflows.md                    # Standard workflows explained
│   ├── enum-reference.md               # All enum values across all databases
│   └── troubleshooting.md              # Common issues
│
└── examples/
    ├── sample-config.json              # Example filled-in config as reference
    └── sample-prompts.md               # Example things to ask the agent
```

## Single-Project Architecture

One Claude Project handles everything: onboarding on first use, then daily operations forever after.

### How it works

The system prompt has three modes baked in:

1. **Onboarding mode** — Triggered when no config.json exists in Project Knowledge, or when the user says "set up" / "configure" / "onboard". The agent walks the user through setup by building real data.

2. **Migration mode** — Triggered when the user says "migrate" / "import" / "sync" or when offered during onboarding. The agent scans databases in other teamspaces, builds a schema mapping, and migrates data in user-approved batches.

3. **Operations mode** — The default once config exists. Agent creates tickets, spawns tasks, links databases, follows all conventions.

The transition is seamless. The onboarding ends by generating a config.json that the user uploads to Project Knowledge. From that point on, the agent operates in production mode.

### The Onboarding Flow (~5 minutes)

#### Step 1: Discover & Verify (~1 min)
- Agent introduces itself, confirms Notion MCP is connected
- Fetches the OS Layer page, discovers all 8 database data source IDs
- Verifies relations are intact (should be fine from template duplication)
- Maps everything internally

#### Step 2: Personalize (~2 min)
- "What's your company name?" → Sets workspace identity
- "What short prefix for internal codes? (e.g., AC-, MV-)" → Sets code prefix
- Agent maps team members via Notion MCP get-users
- Agent generates the standard internal project codes with their prefix
- Asks which apply, lets user add/remove
- Asks about clients → generates client codes
- Adds all codes to Tickets database enum via MCP

#### Step 2.5: Offer Migration (optional, ~5-10 min)
- "Do you have existing databases in another teamspace you'd like to bring in?"
- If yes: agent scans source teamspace, builds schema mapping, migrates in batches
- Migrated data replaces the "learn by doing" step — real data IS the tutorial
- If no: proceed to Step 3

#### Step 3: Learn by Doing (~2 min) — skipped if migration was performed
- "What are you working on this week? Let's create your first ticket."
- Agent suggests Project Code, Priority, links
- User confirms → agent creates ticket
- "What's the first action item?" → spawns a task, links to ticket
- One cycle through the workflow teaches the whole system

#### Step 4: Generate Config (~30 sec)
- Agent generates config.json with all real IDs, codes, users
- Provides it for download
- "Upload this to Project Knowledge — I'll use it from now on."
- Done. Agent is now in operations mode.

## Key Design Decisions

### Why one project?

- Less friction — no second project to create
- Onboarding is short (~5 min), doesn't pollute context
- Config.json in Project Knowledge is the "switch"
- Users can re-run onboarding anytime by saying "reconfigure"

### Why pre-built databases + agent customization?

- Notion template gives users the full visual structure immediately
- Relations, views, formulas all transfer via duplication
- Agent just customizes enums, verifies relations, and populates

### Why learn-by-doing?

- Users understand the ticket→task flow by doing it once
- The first ticket IS real work, not a tutorial
- No separate documentation needed — the setup IS the tutorial

## Notion Template Requirements

The public Notion template includes:

### 8 Databases with full schemas
1. 🎫 Tickets — all properties, views, Serialized ID formula
2. ✅ Tasks — all properties, 13 views, Task ID formula
3. 📁 Active Engagements — all properties, Engagement ID formula
4. 🚀 Initiatives — all properties, RICE Score formula
5. 🧩 Internal Projects — all properties, Project ID formula
6. 💼 Client Database — all properties
7. 🤝 Partnerships — all properties
8. 📊 OKRs — all properties, Progress formula

### All relations pre-wired (two-way where applicable)
### All views pre-built
### Hub pages (OS Layer, Dashboard, Team, OKRs, Partnerships)
### Documentation pages with PLACEHOLDER values
### Project Codes: EMPTY (agent fills during onboarding)

## What Needs to Be Built

### Immediate (to ship v1.0)
- [x] Project plan (this document)
- [x] System prompt (with onboarding + operations modes)
- [ ] Config template JSON (with {PLACEHOLDERS})
- [ ] Clean Notion template (strip all Quipos-specific data)
- [ ] README.md
- [ ] SETUP.md (3 steps: duplicate template, create Claude Project, paste prompt)
- [ ] Architecture docs

### v1.1 — External Source Migration
- [x] Migration spec (tentacles-migration-spec.md)
- [x] System prompt v1.1 (with migration mode)
- [x] Config template v1.1 (with migrations section)
- [x] Agent patterns for migration workflows
- [ ] End-to-end migration testing
- [ ] Incremental sync testing

### Future
- [ ] Multiple agent framework support (Goose, custom MCP agents)
- [ ] Template variants (solo founder, small team, agency)
- [ ] Notion automation recipes
- [ ] Video walkthrough
