# Architecture

## Overview

Tentacles is 8 interconnected Notion databases plus a Claude AI agent. The databases form the "OS Layer" — a structured backbone for tracking work from strategy to execution. The agent reads and writes to all 8 databases via Notion's MCP integration, using a config file stored in Claude Project Knowledge to operate without re-discovering the workspace on every session.

---

## The 8 Databases

### 🎫 Tickets

The universal intake layer. Every unit of work starts here — client requests, internal tasks, agent-initiated work, anything. Tickets are the hub that all other databases connect through.

**Key properties:** Title, Project Code (select), Priority (P0–P3), Status, Description, Source (Human/Agent/Email/Slack), Requester, Assignee, Due Date, Serialized ID (formula), Ticket ID (formula)

**Relations:** Tasks, Engagements, Initiatives, Internal Projects, Clients

---

### ✅ Tasks

The execution layer. Tasks are always spawned from a ticket. They represent discrete, actionable work items — the concrete steps that move a ticket to Done.

**Key properties:** Task Name, Status (Backlog → Done), Priority (Urgent/High/Medium/Low), Sprint, Effort Estimate (XS–XL), Task Type, Assignee, Due Date, Completed Date, Is Milestone

**Relations:** Source Ticket (back to Tickets), Engagements, Initiatives, Internal Projects, Partnerships, OKRs, Parent Task / Subtasks (self-referencing)

---

### 📁 Engagements

Client engagement tracking. An Engagement represents an active or historical working relationship with a client — it ties financial data (Contract Value, Billed to Date), timeline, and type to a specific client.

**Key properties:** Engagement Name, Status (Lead → Completed/Lost), Engagement Type (Strategy/Implementation/Advisory/Retainer), Contract Value, Billed to Date, Start Date, End Date, Engagement ID (formula), Remaining Value (formula)

**Relations:** Client (to Clients DB), Tickets

---

### 🚀 Initiatives

Strategic pipeline and evaluation. Initiatives are ideas, opportunities, or strategic bets that need to be scored, approved, and potentially converted into engagements or projects.

**Key properties:** Initiative Name, Status (Idea → Completed/Rejected), Category, Initiative Type, Time Horizon, Impact, Confidence, Reach, Effort, RICE Score (formula), Initiative ID (formula)

**Relations:** Related Client, Linked OKR, Converted to Engagement, Tickets

---

### 🧩 Internal Projects

Internal project delivery. Where internal work actually lives and gets executed. Projects are linked to initiatives (where they came from) and OKRs (what they move).

**Key properties:** Project Name, Status (Not Started → Archived), Project Type, Priority

**Relations:** Tasks, Related Engagement, Related Initiative, Related OKR, Tickets

---

### 💼 Clients

Client and lead CRM. Tracks all current clients, past clients, and leads in the pipeline.

**Key properties:** Name, Status (New Lead → Closed Won/Lost), Source, Probability, Value

**Relations:** Tickets

---

### 🤝 Partnerships

External partner relationship tracking. Covers referral partners, co-delivery partners, technology integrations, and marketing relationships.

**Key properties:** Partner Name, Partnership Stage (Prospect → Closed), Partnership Type

**Relations:** Related Clients, Related Initiatives

---

### 📊 OKRs

Strategic objectives and key results. Self-referencing structure — Key Results link to their parent Objective. Tracks progress numerically (Start Value, Current Value, Target Value) with a Progress formula.

**Key properties:** title (OKR name), Type (Objective/Key Result), Status (On Track → Completed), Level (Company/Team/Individual), Time Period, Start Value, Current Value, Target Value, Progress (formula)

**Relations:** Parent Objective (self), Related Engagements

---

## Relation Map

Every database can reach every other within 1-2 hops. Tickets are the universal hub.

```
Tickets ←→ Tasks
Tickets ←→ Engagements ←→ Clients
Tickets ←→ Initiatives ←→ OKRs
Tickets ←→ Internal Projects ←→ Tasks
Tickets ←→ Clients

Tasks → Engagements, Initiatives, Internal Projects, Partnerships, OKRs
Initiatives → Clients, Engagements (via Converted to Engagement)
Internal Projects → Engagements, Initiatives, OKRs
Partnerships → Clients, Initiatives
OKRs → OKRs (self-referencing parent/child)
```

---

## The Agent

### Two-Mode Architecture

**Onboarding Mode** — triggered when no config file is in Project Knowledge, or when the user says "reconfigure." The agent:
1. Discovers and verifies all 8 databases in the correct teamspace
2. Personalizes the system (company name, prefix, project codes, team members)
3. Updates the documentation pages in the Notion template with real values
4. Teaches the system by creating the user's first real ticket and task
5. Generates a config JSON file for the user to upload to Project Knowledge

**Operations Mode** — triggered when a config file is present. The agent loads the config and operates with full knowledge of all database IDs, enums, relations, and conventions. No re-discovery required.

### Safety Rule

The agent never modifies pages, databases, or content outside the user's Tentacles teamspace. Before any write operation, it verifies the target belongs to the correct teamspace. Users may have other teamspaces with live production data — the agent scopes all operations tightly.

---

## Config File

The config JSON (generated during onboarding, stored in Project Knowledge) contains:

- **Database IDs and data source IDs** for all 8 databases — used in every MCP call
- **Enum values** for every select/multi-select property — ensures exact string matches
- **Relation mappings** — property names and which database each relation points to
- **Project codes** — internal prefix, all internal codes, all client codes
- **Users** — name to Notion user ID mapping
- **Conventions** — ticket ID format, child DB naming, date format, error handling rules
- **Workflows** — short descriptions of the 5 standard workflows

The config is what makes the agent fast and reliable. Without it, every session would require re-discovering the workspace. With it, the agent operates immediately with full context.

---

## Conventions

### Ticket ID Format
`<Project Code>-<zero-padded number>`

Example: `AC-OPS-001`, `PERS-042`

### Child Database Naming
`<Serialized ID>-<Purpose>`

Example: `AC-OPS-001-Deliverables`, `PERS-042-Meeting Notes`

Child databases are nested under their originating ticket page and must include a Source Ticket relation back to the Tickets database.

### Date Format
All date properties use expanded Notion MCP format:

```json
"date:Due Date:start": "2026-04-01",
"date:Due Date:end": null,
"date:Due Date:is_datetime": 0
```

Plain date strings like `"2026-04-01"` do not work with the Notion MCP.

### Relations
Relations are set as JSON arrays of Notion page URLs:

```json
"[\"https://www.notion.so/abc123...\"]"
```

### Activity Log
Every completed workflow ends with a summary comment posted to the source ticket. Errors are logged as comments with ISO 8601 timestamps. The ticket's comment thread is the agent's audit trail.
