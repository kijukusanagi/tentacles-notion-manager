# Tentacles — Agent Patterns

Practical workflows you can run with your Tentacles agent once onboarding is complete. These aren't features you need to configure — they're things you can ask the agent to do in conversation. Copy-paste any prompt below or adapt it to your situation.

---

## Triage

### Triage All New Tickets

> "Triage all my New tickets — review each one, suggest a priority and project code, and flag anything that's missing a description or assignee."

The agent queries the Tickets database filtered to Status = New, reviews each ticket's content, and presents recommendations for priority assignment, project code mapping, and any gaps. You confirm or adjust, then the agent updates each ticket to Status = Triaged.

**When to use:** Start of day, or after a batch of intake comes in from email, Slack, or manual entry.

### Sort Incoming by Urgency

> "What are my P0 and P1 tickets right now? Any of them blocked?"

Quick status check. The agent pulls high-priority tickets and cross-references for Blocked status, missing assignees, or stale items (no update in 3+ days).

---

## Daily Operations

### Morning Check-In

> "Give me a morning briefing — what's in progress, what's blocked, and what's due this week?"

The agent queries across Tickets (In Progress, Blocked), Tasks (In Progress, due dates this week), and Engagements (Active) to build a snapshot of your current state. No ticket is created for this — it's a read-only summary.

### Create a Ticket from a Conversation

> "I just got off a call with [client]. They need [thing]. Can you ticket that up?"

The agent suggests the right project code (matching the client code if one exists), writes a description from your summary, sets priority, links the client record, and creates the ticket. Then asks if you want to spawn tasks from it.

### Spawn a Batch of Tasks

> "Break down ticket [ID] into tasks: first we need to do X, then Y, then Z."

The agent creates multiple tasks linked to the source ticket, each with sequential priorities and suggested effort estimates. All tasks land in the current sprint or backlog depending on your preference.

---

## Weekly Reviews

### Weekly Rollup

> "Do a weekly rollup — what got done this week, what's still open, and what moved to blocked?"

The agent queries all databases for changes in the last 7 days: tickets moved to Done or Closed, tasks completed, engagements updated, initiatives that changed status. It presents a structured summary organized by project code.

**Tip:** Ask for this every Friday. Over time, these rollups become your project history.

### Stale Item Sweep

> "Find anything stale — tickets or tasks that haven't been updated in more than 2 weeks."

The agent scans for items with no status change or comment activity in 14+ days. It presents them grouped by database and suggests actions: close, re-prioritize, or flag for discussion.

---

## Strategic Reviews

### Initiative Pipeline Review

> "Show me the initiative pipeline — what's under evaluation, what's approved, and what's the RICE ranking?"

The agent queries the Initiatives database, groups by status, and presents RICE scores for anything under evaluation or approved. Useful for quarterly planning or when deciding what to invest in next.

### OKR Progress Check

> "How are we tracking on OKRs this quarter?"

The agent pulls all OKRs for the current time period, shows progress percentages (Current Value vs Target Value), and flags anything marked At Risk or Behind.

### Client Health Overview

> "Give me a client health check — active engagements, any at risk, and total pipeline value."

Cross-references Clients, Engagements, and Tickets to show: active engagement count per client, any engagements On Hold or with overdue tickets, and aggregate contract values.

---

## Multi-Step Workflows

### New Client Onboarding

> "We just closed [client name]. Set them up in the system."

The agent runs the full client onboarding workflow: updates the client record to Closed Won, creates a new engagement, suggests a project code for the client, adds it to the Tickets enum, and creates the initial onboarding tickets (kickoff, access setup, first deliverable).

### Initiative to Project Conversion

> "Approve the [initiative name] initiative and spin it up as a project."

The agent updates the initiative status to Approved, creates a corresponding Internal Project, generates a project code if needed, creates the first set of tickets, and links everything together.

### End-of-Sprint Closeout

> "Close out Sprint 1 — mark all Done tasks as complete, roll anything unfinished to Sprint 2, and give me a summary."

The agent updates task sprint assignments, logs completion dates, moves incomplete work forward, and creates a summary ticket documenting what shipped and what carried over.

---

## Data Migration

### Migrate from an Existing Teamspace

> "Scan my Operations teamspace and bring everything into Tentacles."

The agent discovers all databases in the target teamspace, inspects their schemas, and builds a mapping to the OS Layer. It presents a batch migration plan ordered by dependency (clients first, then engagements, then tickets, then tasks). You review and approve each batch before anything gets created. Every migrated record gets a provenance tag so you know where it came from.

**When to use:** First setup when you have existing Notion data, or when you've been managing work in a separate teamspace and want to consolidate.

### Selective Migration

> "Just bring in the clients and active projects from my Sales teamspace — skip everything else."

You can scope migration to specific databases or filter by status. The agent adjusts the scan and plan accordingly.

### Incremental Sync

> "Sync — anything new in my old Operations teamspace?"

After an initial migration, the agent checks registered sources for records created or modified since the last sync. New items get proposed as a batch; changed items show a diff for your review. Nothing updates without your approval.

**When to use:** Transition period when some team members are still using the old system, or when external data flows into a source teamspace.

### Adjust Migration Mappings

> "The status mapping for the Project Tracker import isn't right — 'Paused' should map to 'On Hold', not 'Blocked'."

You can adjust property and enum mappings after the initial migration. The agent updates the config for future syncs.

---

## Tips

**Be specific about scope.** "Triage my tickets" is good. "Triage the three New tickets from this week" is better — it keeps the agent focused and the confirmation step manageable.

**Use project codes as filters.** "Show me everything under AC-WEB" is a fast way to get a project-specific view without navigating Notion.

**Chain requests naturally.** After a rollup, you might say "OK, close tickets 4 and 7, and re-prioritize ticket 12 to P1." The agent handles sequential operations in conversation.

**The agent always confirms before writing.** You'll never be surprised by a change — every create and update gets presented for approval first.

---

## Effort Tracking (v1.2)

### Log Time on a Task

> "Log 3 hours on the API integration task"

The agent finds the task, adds hours to the Hours Spent field, and confirms. If hours were already logged, it adds to the existing total.

### Time Report for a Project

> "How much time have we spent on the website redesign?"

The agent traces all tasks linked to the project (via tickets and direct relations), sums Hours Spent, and presents a breakdown by person and by task. Includes estimated vs actual variance if both values exist.

### Sprint Time Summary

> "How many hours did we log this sprint?"

The agent queries all tasks in the current sprint with Hours Spent > 0, groups by assignee, and shows per-person totals against sprint capacity.

### Effort Variance Check

> "Which tasks ran over their estimates?"

The agent finds tasks where Hours Spent exceeds Hours Estimated by more than the configured threshold, shows the variance, and identifies patterns (e.g., "L tasks consistently take 12h instead of 8h — consider adjusting your mapping").

---

## Proactive Alerting (v1.2)

### Morning Briefing with Alerts

> "Morning briefing"

The agent runs all enabled health checks first, presents the alert summary grouped by severity (🔴 critical → 🟡 warning → 🔵 info), then delivers the standard briefing. Critical alerts appear at the top so nothing gets buried.

### Dedicated Health Check

> "Run a health check" / "Any problems I should know about?" / "What needs attention?"

Full scan across all 8 databases. The agent presents findings grouped by severity and offers to take action on any critical or warning items — reassign work, create follow-up tickets, or update stale items.

### Targeted Alert Check

> "Are any engagements falling behind on billing?"

The agent runs the specific relevant check and presents results. Useful when you have a hunch about a specific area and want the agent to validate it.

### Suppress an Alert

> "The Acme engagement is intentionally unbilled — ignore it in alerts"

The agent notes the exception. You can manage alert suppressions to reduce noise on items you're aware of.

---

## Capacity Planning (v1.2)

### Team Capacity Dashboard

> "Show me team capacity" / "Team workload"

Per-person breakdown of sprint load vs capacity with color-coded utilization status. Shows active task count, estimated hours assigned, and remaining bandwidth.

### Find Available Bandwidth

> "Who has bandwidth?" / "Who can take on more work?"

Quick filter for team members under 60% utilization, showing their remaining hours and current active tasks.

### Smart Assignment

> "Assign the API refactor task to Jordan"

Agent checks Jordan's current load before assigning. If over the warning threshold, it warns and suggests who else has bandwidth. Proceeds with assignment only after user confirms.

### Sprint Planning Assistant

> "Help me plan Sprint 2" / "What fits in this sprint?"

Agent shows remaining team capacity, pulls prioritized backlog items with effort estimates, and suggests which items fit. Accounts for per-person capacity limits, not just team totals.

### Velocity Report

> "How did we do last sprint?" / "Sprint velocity"

Compares planned vs actual: tasks completed, hours estimated vs hours spent, per-person performance. Shows trend across recent sprints if historical data exists.

### Rebalance Sprint

> "Rebalance the sprint" / "Redistribute work"

Agent identifies overloaded team members, suggests task reassignments to balance utilization across the team, and shows before/after capacity percentages for each proposed change.

---

## Granular Dives (v1.3)

### Research Dive

> "Dive into CRM options for our team — I want to compare at least 5 platforms."

The agent creates a ticket, selects the Research & Analysis template, builds a child database with one row per CRM platform, and works through each one: pulling key features, pricing, strengths, and weaknesses. Ends with a ranked recommendation.

**When to use:** Evaluating vendors, investigating technologies, market research, competitive analysis, due diligence on potential partners.

### Decision Matrix Dive

> "Help me decide between hiring a contractor vs bringing on a full-time engineer."

The agent asks what criteria matter (cost, speed, quality, cultural fit, etc.), sets up a scored matrix, walks through each option against each criterion, and produces a weighted recommendation with the reasoning laid out.

**When to use:** Any decision with multiple options and competing criteria — tool selection, build vs buy, strategic direction, hiring decisions.

### Project Plan Dive

> "I need to plan the Q2 product launch — break it down into everything we need to do."

The agent creates a phased breakdown: pre-launch, launch week, post-launch. Each phase gets milestones, deliverables, owners, time estimates, dependencies, and risks. After approval, offers to spawn the plan as real tickets and tasks in Tentacles.

**When to use:** Large initiatives that need structured decomposition before work begins. Architecture planning, launch planning, roadmap development.

### Content Workshop Dive

> "I need to write a proposal for the Acme partnership. Let's build it out section by section."

The agent outlines the proposal structure (exec summary, problem statement, proposed solution, timeline, pricing, next steps), then drafts each section for review. User provides feedback per section; agent revises. Final deliverable is compiled and attached to the ticket.

**When to use:** Proposals, reports, articles, presentation outlines, pitch deck structure, any deliverable that benefits from structured section-by-section development.

### Audit Dive

> "Audit our active engagements — which ones are healthy and which need attention?"

The agent pulls all active engagements, creates one row per engagement in the audit child DB, evaluates each against criteria (billing status, recent activity, ticket volume, deadline proximity), and produces a severity-coded report with action items.

**When to use:** Periodic reviews, process audits, data quality checks, compliance reviews, portfolio health assessments.

### Resume a Dive

> "Where did we leave off on the CRM evaluation?"

The agent finds the active dive, reads the child database state, and picks up exactly where the last session ended. Shows progress and suggests the next item to work on.

**When to use:** Any time you want to continue a dive that was paused or interrupted. Dives persist in Notion, so you can come back days or weeks later.

### Quick Dive (Lightweight)

> "Quick dive — what are our options for a team offsite venue? Just need 3-4 options compared."

For smaller scope, the agent still creates the child DB but keeps it lean — fewer items, faster evaluation. The dive might complete in a single session.

**When to use:** Lighter decisions or quick research that still benefit from structured comparison rather than a freeform conversation.

### Plan-to-Execution Dive

> "Take the project plan dive we did for the Q2 launch and turn it into real tickets and tasks."

After a Project Plan dive is complete, the agent can spawn the plan items as actual Tentacles tickets and tasks — with effort estimates, assignees, and dependencies all carried over from the dive. This is the bridge between planning and execution.

**When to use:** After completing a Project Plan dive, when you're ready to start work.
