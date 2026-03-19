# Workflows

Tentacles has five standard workflows that cover the vast majority of operational scenarios. Every workflow starts with a ticket.

---

## Smart Ticket Creation

Before any workflow executes, the agent uses a suggest-confirm-create-report cycle:

1. **Parse intent** — the agent understands what you're asking for
2. **Search databases** — looks up relevant clients, engagements, initiatives, and projects in Notion
3. **Suggest** — presents a full ticket or task with Title, Project Code (best match), Priority (with brief justification), Description, and any relevant cross-links
4. **Confirm** — you approve or adjust before anything is created
5. **Create** — executes via MCP
6. **Report** — shows the Serialized ID and what was linked

This prevents garbage data. You always see exactly what the agent is about to create before it creates it.

---

## 1. Client Request

**When to use:** A client asks for something — a deliverable, a change, a meeting, anything that represents work tied to a client relationship.

**Flow:** Create ticket → Triage → Spawn tasks → Execute → Done

1. A ticket is created with the client's project code, linked to the client in the Clients database and (if applicable) their active engagement
2. The ticket is triaged — Priority is set, it moves from New to Triaged
3. Tasks are spawned from the ticket: each concrete action item becomes a Task linked back via Source Ticket relation
4. Tasks move through Backlog → To Do → In Progress → Done as work progresses
5. When all tasks are done, the ticket moves to Done; a summary comment is posted

**Databases involved:** Tickets, Tasks, Clients, Engagements

---

## 2. Initiative Approval

**When to use:** A strategic idea or opportunity has been evaluated and approved — now it needs to become real work with a project code and tickets.

**Flow:** Update initiative → Create project → Add project code → Create tickets → Spawn tasks

1. The Initiative's Status is updated from Under Evaluation or Approved to In Progress
2. A new Internal Project is created in the Internal Projects database and linked to the Initiative
3. A new project code is added to the Tickets database enum (if this initiative warrants its own code — often it uses an existing one like OPS or a relevant client code)
4. Tickets are created for the key workstreams of the initiative
5. Tasks are spawned from each ticket

**Databases involved:** Initiatives, Internal Projects, Tickets, Tasks, OKRs (if the initiative is linked to a key result)

---

## 3. Client Onboarding

**When to use:** A prospect converts to a paying client — it's time to set up their engagement, project infrastructure, and kickoff work.

**Flow:** Update client to Closed Won → Create engagement → Create project code → Create onboarding tickets

1. The client's Status in the Clients database is updated to Closed Won
2. A new Engagement is created in the Engagements database: linked to the client, Status = Active, Contract Value set, Start Date set
3. A client project code is created (e.g., PERS for Persimmon Capital) and added to the Tickets database enum
4. Onboarding tickets are created: kickoff, access provisioning, first deliverable, etc. — all using the client's new code
5. Tasks are spawned from each ticket

**Databases involved:** Clients, Engagements, Tickets, Tasks, Internal Projects (if an internal project is created for the engagement)

---

## 4. Agent Autonomous Work

**When to use:** The agent is doing work on its own — running a scheduled review, executing a multi-step process, building something in a child database. The agent needs to account for its own work just like humans do.

**Flow:** Create ticket (Source=Agent) → In Progress → Create child DB (if needed) → Do work → Summary comment → Done

1. The agent creates a ticket with Source = "Agent" and Requester = "tentacles-agent" — this is the paper trail for all agent-initiated work
2. The ticket moves to In Progress immediately
3. If the work requires structured data, the agent creates a child database nested under the ticket page, named `{Serialized ID}-{Purpose}` (e.g., `AC-AGENT-003-Audit Results`)
4. The agent does the work, writing results to the child DB or as page content on the ticket
5. A summary comment is posted to the ticket with a timestamp and outcome
6. The ticket moves to Done

**Databases involved:** Tickets, plus any child databases created for the work

---

## 5. Periodic Review

**When to use:** Regular cadence check — weekly, monthly, or on demand. Surfaces stale work, blocked items, and things that have fallen through the cracks.

**Flow:** Query all DBs → Flag stale items → Create summary ticket

1. The agent queries all 8 databases for stale or concerning items: tickets stuck in New for >7 days, tasks in To Do without an assignee, engagements with no recent ticket activity, initiatives that were Approved but never moved, OKRs that are At Risk or Behind
2. Flagged items are noted — the agent may add comments directly to stale items or update their status to reflect current reality
3. A summary ticket is created (Source = Agent, Project Code = {PREFIX}-OPS or {PREFIX}-TKT) with the full review as the description
4. The user reviews the summary ticket and decides what to action

**Databases involved:** All 8
