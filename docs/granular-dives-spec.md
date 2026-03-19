# Tentacles v1.3 — Granular Dives

## Overview

Granular Dives turn Tentacles into a deep work partner. When a ticket warrants structured analysis — research, planning, content creation, decision-making — the agent spins up a dedicated child database under that ticket, populates it with structured items, and works through them interactively with the user. Dives are resumable across conversations and tracked on the parent ticket.

This formalizes the "Agent Autonomous" workflow's child database pattern into a full-featured system with session management, dive templates, and cross-conversation continuity.

---

## Design Principles

1. **Every dive lives under a ticket.** No orphan deep work. The ticket is the anchor — it tracks dive status, links the child DB, and receives the summary when the dive closes.

2. **Structured, not freeform.** A dive isn't a Notion page with paragraphs. It's a database where each row is a discrete item — a research finding, a decision option, a planning step, a content section. This makes the work scannable, sortable, and actionable.

3. **Resumable by default.** The child DB persists in Notion. The parent ticket tracks dive state. Any new conversation can pick up where the last one left off by reading the child DB's current contents.

4. **Agent suggests, user decides.** The agent proactively suggests dives when it detects complexity, but never forces one. The user can always say "just create the ticket" or "I don't need a deep dive on this."

5. **Templates accelerate, not constrain.** Pre-built dive templates (research, decision matrix, project plan, etc.) give the child DB an instant schema. But the user can always customize or go freeform.

---

## How It Works

### Activation

A dive activates in three ways:

**1. User triggers explicitly:**
- "Dive into [topic]"
- "Go deep on [ticket]"
- "Let's do a deep dive on [X]"
- "Research [topic] for me"
- "Help me think through [decision]"
- "Break down [project] in detail"
- "Start a dive session on [ticket ID]"

**2. Agent suggests proactively:**

During ticket creation or triage, if the agent detects signals that a ticket would benefit from structured deep work, it offers:

"This looks like it could use a deeper dive — there are multiple angles to evaluate and some research involved. Want me to spin up a structured analysis session? I'll create a child database under this ticket where we can work through it systematically."

**Suggestion triggers:**
- Ticket description contains decision language ("should we," "evaluate," "compare," "which option")
- Ticket involves multiple stakeholders or competing priorities
- Ticket scope is broad or ambiguous (agent would need to ask 5+ clarifying questions)
- Ticket type is "Decision" or "Proposal"
- User asks a question that implies research ("what are the options for," "how do competitors handle")

**3. Resume an existing dive:**
- "Resume the dive on [ticket/topic]"
- "Where did we leave off on [topic]?"
- "Pull up the [topic] analysis"
- "Continue the deep dive"

### Dive Lifecycle

```
TRIGGER → SELECT TEMPLATE → CREATE CHILD DB → POPULATE → WORK SESSION(S) → SYNTHESIZE → CLOSE
```

**1. Trigger** — User requests or agent suggests a dive. If no parent ticket exists, create one first.

**2. Select Template** — Agent suggests the best-fit dive template based on the topic. User confirms or picks a different one. (See Dive Templates below.)

**3. Create Child DB** — Agent creates a database nested under the parent ticket page, following existing child DB conventions:
  - Name: `{Serialized ID}-{Dive Purpose}` (e.g., `QP-OPS-042-Vendor Evaluation`)
  - Required props: Name (title), Status, Created Date, plus template-specific properties
  - Source Ticket relation back to the parent ticket

**4. Populate** — Agent creates the initial set of rows. For research dives, these might be competitors or options to evaluate. For planning dives, these are phases or workstreams. The user can add, remove, or reorder items.

**5. Work Session(s)** — The interactive phase. Agent and user work through items together. This may span multiple conversations. Between sessions, the child DB in Notion holds all state — the agent reads it on resume.

**6. Synthesize** — When the dive is complete (or the user says "wrap it up"), the agent produces a synthesis: a summary comment on the parent ticket distilling the dive's findings, recommendations, or deliverables.

**7. Close** — Parent ticket gets a status update. The child DB remains as a permanent record nested under the ticket.

---

## Dive Templates

Templates define the child database schema for different types of deep work. Each template specifies the properties, initial structure, and agent behavior for that dive type.

### 1. Research & Analysis

**Use when:** Evaluating vendors, analyzing competitors, investigating technologies, market research, due diligence.

**Child DB Schema:**
| Property | Type | Purpose |
|----------|------|---------|
| Name | title | Research item name (company, product, technology, etc.) |
| Status | select: Not Started, In Progress, Evaluated, Flagged | Tracking progress through each item |
| Category | select | User-defined grouping (auto-suggested from the topic) |
| Score | number (1-10) | Overall evaluation score |
| Strengths | rich_text | What's good about this option |
| Weaknesses | rich_text | What's concerning |
| Notes | rich_text | Additional findings, links, context |
| Recommendation | select: Strong Yes, Lean Yes, Neutral, Lean No, Strong No | Agent's assessment after evaluation |
| Source | url | Primary reference link |

**Agent behavior:**
- Populates initial items from the user's request or web research
- For each item, the agent researches (using web search if available) and fills in Strengths, Weaknesses, Notes
- Presents findings item by item or as a comparison table
- Scores and recommends after evaluating all items
- Synthesis: ranked recommendation with top pick and reasoning

### 2. Decision Matrix

**Use when:** Choosing between options where multiple criteria matter. Hiring decisions, tool selection, strategic direction, build vs buy.

**Child DB Schema:**
| Property | Type | Purpose |
|----------|------|---------|
| Name | title | Option being evaluated |
| Status | select: Not Scored, Scored, Eliminated, Selected | Decision progress |
| {Criterion 1} | number (1-5) | Dynamically named — one property per criterion |
| {Criterion 2} | number (1-5) | Each criterion becomes a scored column |
| {Criterion N} | number (1-5) | Agent suggests criteria, user confirms |
| Weighted Score | number | Calculated by agent (criteria × weights) |
| Pros | rich_text | Key advantages |
| Cons | rich_text | Key disadvantages |
| Notes | rich_text | Additional context |

**Agent behavior:**
- Asks user for: options to evaluate, criteria that matter, and optional weights per criterion
- Creates one row per option, one column per criterion
- Walks through scoring interactively: "How does [Option A] rate on [Criterion]? (1-5)" or offers to suggest scores based on research
- Calculates weighted totals
- Synthesis: clear recommendation with the math shown, plus qualitative factors that the numbers don't capture

### 3. Project Plan / Breakdown

**Use when:** Decomposing a large initiative into phases, milestones, and workstreams. Architecture planning, launch planning, roadmap development.

**Child DB Schema:**
| Property | Type | Purpose |
|----------|------|---------|
| Name | title | Phase, milestone, or workstream name |
| Status | select: Planning, Ready, In Progress, Complete, Blocked | Phase status |
| Phase | select | Top-level grouping (Phase 1, Phase 2, etc.) |
| Dependencies | relation (self) | What must complete before this starts |
| Estimated Duration | text | Time estimate (e.g., "2 weeks", "3 days") |
| Estimated Hours | number | Hour estimate (ties into effort tracking) |
| Owner | text | Suggested assignee |
| Deliverables | rich_text | What this step produces |
| Risks | rich_text | What could go wrong |
| Notes | rich_text | Additional context |

**Agent behavior:**
- Starts with the user's high-level goal, breaks it into phases
- For each phase, identifies key milestones and workstreams
- Maps dependencies between items
- Estimates effort for each item (using the effort mapping from config)
- Identifies risks and blockers
- Synthesis: phased timeline, critical path, total effort estimate, risk summary
- **Offer to spawn:** After the plan is approved, offer to create real Tickets and Tasks from the plan items: "Want me to turn these into actual tickets and tasks? I'll create them in dependency order with the effort estimates we worked out."

### 4. Content Workshop

**Use when:** Drafting a proposal, report, article, presentation outline, pitch deck structure, or any deliverable that benefits from structured section-by-section development.

**Child DB Schema:**
| Property | Type | Purpose |
|----------|------|---------|
| Name | title | Section or component name |
| Status | select: Outline, Draft, Review, Final | Content readiness |
| Order | number | Sequence in the final deliverable |
| Section Type | select: Intro, Body, Analysis, Conclusion, Appendix, Visual | Content type |
| Key Points | rich_text | Bullet points this section must cover |
| Draft Content | rich_text | The actual drafted text |
| Feedback | rich_text | User feedback / revision notes |
| Word Count | number | Target or actual word count |

**Agent behavior:**
- Builds an outline first (items = sections), gets user approval on structure
- Drafts each section, presents for review
- Iterates based on feedback — user can mark sections for revision
- Tracks overall progress via Status field across all sections
- Synthesis: compile the full deliverable in the parent ticket's description or as a linked document, with a summary comment noting the development process

### 5. Audit / Review

**Use when:** Systematic review of existing work, processes, systems, or data quality. Security audits, process reviews, data cleanup, compliance checks.

**Child DB Schema:**
| Property | Type | Purpose |
|----------|------|---------|
| Name | title | Item being audited |
| Status | select: Not Reviewed, In Review, Pass, Fail, Needs Action | Audit result |
| Category | select | Grouping (user-defined or auto-detected) |
| Severity | select: Critical, Warning, Info, OK | Issue severity |
| Finding | rich_text | What was found |
| Recommendation | rich_text | What to do about it |
| Action Item | text | Specific next step (can become a task) |
| Evidence | rich_text | Links, screenshots, data supporting the finding |

**Agent behavior:**
- Scans the relevant databases/systems and creates one row per item reviewed
- For each item, assesses against criteria (user-defined or best practice)
- Flags issues by severity
- Produces actionable recommendations
- Synthesis: summary of findings by severity, prioritized action items
- **Offer to spawn:** Create tickets/tasks from action items: "I found 3 critical and 5 warning items. Want me to create tickets for the critical ones?"

### 6. Freeform

**Use when:** None of the above templates fit. The user wants structured deep work but on their own terms.

**Child DB Schema:**
| Property | Type | Purpose |
|----------|------|---------|
| Name | title | Item name |
| Status | select: Pending, In Progress, Complete | Basic progress tracking |
| Category | select | User-defined grouping |
| Priority | select: High, Medium, Low | Item priority |
| Notes | rich_text | Content |
| Created Date | created_time | Auto-set |

**Agent behavior:**
- Asks the user what properties they need beyond the basics
- Adds custom properties via MCP as requested
- Follows the user's lead on structure and workflow
- Still produces a synthesis at the end

---

## Ticket Integration

### Parent Ticket Fields

When a dive is active, the parent ticket reflects it:

- **Type** = the ticket's existing Type (Request, Decision, Proposal, etc.) — no new Type value needed, since dives enhance any ticket type
- **Status** = "In Progress" while the dive is active
- **Description** gets a dive header appended:

```
---
🔬 DIVE SESSION: {template_name}
Status: {Active / Paused / Complete}
Child DB: {link to child database}
Started: {date}
Last Session: {date}
Items: {N total}, {N complete}, {N remaining}
---
```

This block is updated by the agent at the start and end of each session.

### Dive Status on Ticket

The agent tracks dive state using a structured block in the ticket description. This is how cross-conversation resumability works — when the agent loads the ticket, it reads this block to understand where the dive stands.

States:
- **Active** — dive is in progress, items are being worked
- **Paused** — user said "pause" or "come back to this later" — the agent won't auto-resume
- **Complete** — all items processed, synthesis delivered, dive is closed

### Summary Comment on Completion

When a dive completes, the agent adds a summary comment to the parent ticket:

```
🔬 Dive Complete: {template_name}

## Summary
{2-3 paragraph synthesis of findings/results}

## Key Outcomes
- {outcome 1}
- {outcome 2}
- {outcome 3}

## Artifacts
- Child DB: {link} ({N} items)
- {Any other outputs: documents, spawned tickets, etc.}

## Metrics
- Sessions: {N}
- Items processed: {N}
- Time logged: {N}h (if effort tracking is active)

Dive started {start_date}, completed {end_date}.
```

---

## Session Management

### Starting a Session

When the user triggers or resumes a dive, the agent:

1. **Loads context.** Finds the parent ticket, reads the dive status block, fetches the child DB, queries all items with their current statuses.

2. **Presents state.** "Picking up where we left off on the vendor evaluation. You've got 8 items total — 3 evaluated, 2 in progress, 3 not started. Last time we finished scoring Vendor C. Want to continue with Vendor D?"

3. **Proposes next steps.** Based on what's left, suggest the most logical next action — continue evaluating items, revisit flagged items, or start the synthesis if enough is done.

### During a Session

The agent works through dive items interactively:

- Presents items one at a time or in batches (user preference)
- Updates child DB records in real time via MCP as work is completed
- Tracks progress: "That's 5 of 8 evaluated. 3 to go."
- Allows branching: user can skip ahead, go back, add new items, or remove items mid-dive
- Allows pausing: "Let's stop here — I'll pick this up tomorrow" → agent updates dive status to Paused

### Ending a Session

When the user stops mid-dive (explicitly or by ending the conversation):

1. Agent updates the dive status block on the parent ticket with current progress
2. Agent adds a session comment to the ticket: "Session {N}: Evaluated items X, Y, Z. {N} items remaining."
3. Child DB state is already current (updated in real time)

### Closing a Dive

When the dive is complete:

1. Agent runs synthesis (template-specific — see each template's behavior above)
2. Adds the summary comment to the parent ticket
3. Updates dive status to Complete
4. Updates parent ticket Status to Done (or asks user what status it should be)
5. Offers follow-up actions: spawn tasks from findings, create new tickets for action items, etc.

---

## Proactive Dive Suggestions

### When to Suggest

The agent suggests a dive during these operations:

**During ticket creation:**
If the ticket being created matches suggestion triggers (decision language, broad scope, research needed), offer after the ticket is confirmed: "This ticket is created. It looks like it could benefit from a structured dive — want me to set one up?"

**During triage:**
If a triaged ticket is complex, suggest: "This one's got a few moving parts. Want me to spin up a [template] dive to work through it?"

**During health checks:**
If a stale or blocked ticket looks like it stalled due to complexity: "Ticket [X] has been in progress for 12 days with no update. It might benefit from a structured breakdown — want me to start a project plan dive?"

### How to Suggest

Always brief, never pushy:
- Suggest the specific template that fits
- Explain in one sentence what the dive would produce
- Make it easy to decline: "Or I can just leave it as a regular ticket."

---

## Config Additions

```json
{
  "dives": {
    "enabled": true,
    "suggest_proactively": true,
    "suggestion_triggers": {
      "on_decision_tickets": true,
      "on_proposal_tickets": true,
      "on_broad_scope": true,
      "on_stale_complex": true
    },
    "default_template": "freeform",
    "templates": {
      "research": {
        "enabled": true,
        "label": "Research & Analysis",
        "description": "Evaluate options, competitors, or technologies systematically"
      },
      "decision_matrix": {
        "enabled": true,
        "label": "Decision Matrix",
        "description": "Score and compare options across weighted criteria"
      },
      "project_plan": {
        "enabled": true,
        "label": "Project Plan",
        "description": "Break down a large initiative into phased, estimated work"
      },
      "content_workshop": {
        "enabled": true,
        "label": "Content Workshop",
        "description": "Draft deliverables section by section with structured review"
      },
      "audit": {
        "enabled": true,
        "label": "Audit / Review",
        "description": "Systematic review with findings, severity, and action items"
      },
      "freeform": {
        "enabled": true,
        "label": "Freeform",
        "description": "Custom schema — you define the structure"
      }
    },
    "log_effort_on_sessions": true,
    "auto_spawn_tasks": false
  }
}
```

| Field | Type | Default | Purpose |
|-------|------|---------|---------|
| `enabled` | boolean | true | Master switch for dive functionality |
| `suggest_proactively` | boolean | true | Agent offers dives when it detects complexity |
| `suggestion_triggers` | object | all true | Fine-grained control over when suggestions fire |
| `default_template` | string | "freeform" | Template used when user doesn't specify |
| `templates` | object | all enabled | Enable/disable individual templates |
| `log_effort_on_sessions` | boolean | true | Prompt for hours at end of each dive session |
| `auto_spawn_tasks` | boolean | false | Auto-create tasks from dive outputs (vs. offering) |

---

## Migration Registry Entry

```
## v1.2 → v1.3
Summary: Added Granular Dives — structured deep work sessions with child databases.
Schema changes:
  - Tickets: UPDATE "Type" SELECT to add "Dive" option
Config changes:
  - Add top-level `dives` section with template configs and settings
  - databases.tickets.enums.Type: add "Dive"
  - Add `granular_dive` to workflows
  - Update `version` to "1.3"
  - Update `system_prompt_version` to "1.3"
  - Update `changelog`
Steps:
  1. Use MCP update-data-source on Tickets to add "Dive" to Type SELECT
  2. Add dives section to config
  3. Update config version fields
  4. Regenerate config file for user to re-upload
```

---

## System Prompt Additions

### Mode Detection Update

Add to existing mode detection:

```
- **If the user says "dive into", "go deep on", "deep dive", "start a dive",
  "research [X] for me", "help me think through [X]", or "resume the dive":**
  Enter DIVE MODE within Operations Mode. This creates or resumes a
  Granular Dive session on the relevant ticket.
```

### New Section: Granular Dives

```
# ═══════════════════════════════════════════
# GRANULAR DIVES
# ═══════════════════════════════════════════

Granular Dives are structured deep work sessions. The agent creates a child
database under a ticket, populates it with items, and works through them
interactively. Dives are resumable across conversations.

## Activation

Dives activate three ways:

1. **User triggers:** "dive into [X]", "go deep on [X]", "research [X]",
   "help me think through [X]", "start a dive on [ticket]"

2. **Agent suggests:** When creating or triaging a ticket that matches
   suggestion triggers (decision/proposal type, broad scope, research needed),
   offer a dive. Always brief, never pushy.

3. **Resume:** "Resume the dive on [X]", "where did we leave off on [X]",
   "pull up the [X] analysis", "continue the deep dive"

## Starting a Dive

1. **Ensure a parent ticket exists.** If the user says "dive into competitor
   analysis" without referencing a ticket, create one first using the standard
   Smart Ticket Creation flow. Set Type = "Dive" (or the most appropriate type
   if the dive supports a Decision, Proposal, etc.).

2. **Select template.** Present the available templates with one-line
   descriptions. Suggest the best fit based on the topic:

   "What kind of deep work is this?
   - 🔍 **Research & Analysis** — evaluate options or investigate a topic
   - ⚖️ **Decision Matrix** — score options across weighted criteria
   - 📋 **Project Plan** — break down into phases and estimates
   - 📝 **Content Workshop** — draft a deliverable section by section
   - 🔎 **Audit / Review** — systematic review with findings and actions
   - 🆓 **Freeform** — custom structure, you define it

   Or I can pick the best fit — just tell me what you're trying to accomplish."

3. **Create the child database.** Using MCP notion-create-database:
   - Name: `{Serialized ID}-{Dive Purpose}`
   - Parent: the ticket page
   - Schema: per the selected template (see template definitions in config)
   - Always include: Name (title), Status (select), Created Date (created_time)

4. **Update the parent ticket.** Append the dive status block to the
   ticket's description:

   ```
   ---
   🔬 DIVE SESSION: {template_label}
   Status: Active
   Child DB: {link}
   Started: {today}
   Last Session: {today}
   Items: 0 total, 0 complete, 0 remaining
   ---
   ```

5. **Populate initial items.** Based on the template and user's input,
   create the first batch of rows in the child database. Present them
   for confirmation before proceeding to the work phase.

## Working a Dive Session

During an active session:

1. **Present items** one at a time or in overview, based on user preference.
2. **Update records in real time** via MCP as each item is evaluated, scored,
   drafted, or otherwise completed.
3. **Track progress** — mention completion status naturally: "That's 5 of 8
   evaluated."
4. **Allow flexibility** — user can:
   - Skip items: "Skip to vendor E"
   - Add items: "Add another option: [X]"
   - Remove items: "Drop that one, it's not relevant"
   - Reorder: "Let's do the high-priority ones first"
   - Branch: "Actually, let me look at this from a different angle"
5. **Integrate with other Tentacles features:**
   - If effort tracking is enabled and `dives.log_effort_on_sessions` is true,
     prompt for hours at the end of each session
   - If the dive produces action items, offer to spawn tasks
   - If the dive surfaces problems, mention relevant alerts

## Pausing a Dive

When the user pauses ("let's stop here", "come back to this later", end of
conversation with dive in progress):

1. Update all child DB records to reflect current state.
2. Update the dive status block on the parent ticket:
   - Status: Paused (if explicitly paused) or Active (if just ending conversation)
   - Last Session: {today}
   - Items: {updated counts}
3. Add a session comment to the parent ticket:
   "🔬 Session {N}: Worked through items {list}. {N} items remaining.
   Next up: {suggested next item or action}."

## Resuming a Dive

When the user resumes:

1. Find the parent ticket (by topic search, ticket ID, or most recent dive).
2. Read the dive status block to understand state.
3. Fetch the child database and query all items with current statuses.
4. Present a summary: "Picking up the {template} dive on '{topic}'.
   {N} of {total} items complete. Last session we finished {X}.
   Ready to continue with {next item}?"
5. Proceed with the work phase.

If multiple active dives exist, present them: "You have 2 active dives:
1. Vendor evaluation (5/8 complete)
2. Q2 planning (3/12 complete)
Which one?"

## Closing a Dive

When the user says "wrap it up", "synthesize", "close the dive", or when
all items are complete:

1. **Synthesize.** Run the template-specific synthesis:
   - Research: ranked recommendation with reasoning
   - Decision Matrix: winner with scores and qualitative notes
   - Project Plan: phased timeline, critical path, total effort
   - Content Workshop: compiled deliverable
   - Audit: findings summary by severity, prioritized actions
   - Freeform: bullet-point summary of all items and their outcomes

2. **Summary comment** on parent ticket with: synthesis, key outcomes,
   artifacts (child DB link), metrics (sessions, items, time if tracked).

3. **Update dive status** to Complete. Update item counts.

4. **Update ticket status.** Ask: "Want me to mark this ticket as Done,
   or keep it open for follow-up?"

5. **Offer follow-up actions** based on template:
   - Research/Decision: "Want me to create a ticket for implementing
     the selected option?"
   - Project Plan: "Want me to spawn tickets and tasks from this plan?"
   - Audit: "Want me to create tickets for the critical findings?"
   - Content: "Want me to put the final draft in a document?"

## Proactive Suggestions

When `dives.suggest_proactively` is true:

**During ticket creation:** After confirming a new ticket, if it matches
suggestion triggers, offer briefly:
  "This looks like it could benefit from a structured [template] dive.
  Want me to set one up, or just leave it as a regular ticket?"

**During triage:** If a triaged ticket is complex:
  "This one has a few moving parts. A [template] dive might help
  work through it systematically."

**During health checks:** If a stale ticket looks stuck due to complexity:
  "Ticket [X] has been sitting for [N] days — a project plan dive
  might help break it down into actionable pieces."

Never suggest more than once per ticket. If declined, don't ask again.
```

### Workflow Addition

Add to Standard Workflows:

```
- **Granular Dive:** Trigger/suggest dive → Select template → Create child DB → Populate items → Work session(s) → Synthesize → Close → Offer follow-ups
```

---

## Agent Patterns

```markdown
### Research Dive

> "Dive into CRM options for our team — I want to compare at least 5 platforms."

The agent creates a ticket, selects the Research & Analysis template, builds a child database with one row per CRM platform, and works through each one: pulling key features, pricing, strengths, and weaknesses. Ends with a ranked recommendation.

### Decision Matrix Dive

> "Help me decide between hiring a contractor vs bringing on a full-time engineer."

The agent asks what criteria matter (cost, speed, quality, cultural fit, etc.), sets up a scored matrix, walks through each option against each criterion, and produces a weighted recommendation with the reasoning laid out.

### Project Plan Dive

> "I need to plan the Q2 product launch — break it down into everything we need to do."

The agent creates a phased breakdown: pre-launch, launch week, post-launch. Each phase gets milestones, deliverables, owners, time estimates, dependencies, and risks. After approval, offers to spawn the plan as real tickets and tasks.

### Content Workshop Dive

> "I need to write a proposal for the Acme partnership. Let's build it out section by section."

The agent outlines the proposal structure (exec summary, problem statement, proposed solution, timeline, pricing, next steps), then drafts each section for review. User provides feedback per section; agent revises. Final deliverable is compiled and attached to the ticket.

### Audit Dive

> "Audit our active engagements — which ones are healthy and which need attention?"

The agent pulls all active engagements, creates one row per engagement in the audit child DB, evaluates each against criteria (billing status, recent activity, ticket volume, deadline proximity), and produces a severity-coded report with action items.

### Resume a Dive

> "Where did we leave off on the CRM evaluation?"

The agent finds the active dive, reads the child database state, and picks up exactly where the last session ended. Shows progress and suggests the next item to evaluate.

### Quick Dive (Lightweight)

> "Quick dive — what are our options for a team offsite venue? Just need 3-4 options compared."

For smaller scope, the agent still creates the child DB but keeps it lean — fewer items, faster evaluation. The dive might complete in a single session.
```

---

## Integration with Existing Features

### Effort Tracking
- If `dives.log_effort_on_sessions` is true, the agent prompts for hours at the end of each dive session
- Hours are logged on the parent ticket's linked tasks (or on the ticket itself if no tasks exist yet)
- Dive synthesis includes total time invested if effort data exists

### Proactive Alerting
- Active dives with no session in 7+ days get flagged in health checks as stale (using existing stale_tickets check — the parent ticket will trigger it)
- The health check can note: "Ticket [X] has an active dive with 3/8 items remaining — hasn't been touched in 9 days"

### Capacity Planning
- Project Plan dives that produce hour estimates feed into capacity calculations when their items are spawned as tasks
- "Can we fit this project in the sprint?" queries can reference dive-produced estimates

---

## Edge Cases

### Dive on a Ticket That Already Has Tasks
Create the child DB alongside existing tasks. The dive and the tasks are separate work streams under the same ticket. Mention in the dive status block: "Note: this ticket also has {N} linked tasks."

### Multiple Dives on One Ticket
Allowed but uncommon. Each dive gets its own child DB with a distinct name. The dive status block lists all active dives. Agent asks which one when resuming.

### Dive Produces More Work Than Expected
If a dive uncovers significant scope, suggest splitting: "This is turning into a bigger effort than one ticket. Want me to create a new ticket for [sub-topic] and continue the dive there?"

### User Wants to Export Dive Results
The child DB is a real Notion database — it's already exportable via Notion's built-in export. For formatted output, the synthesis comment serves as the summary. For the full deliverable (content workshop), offer to compile into the ticket description or a linked page.

### Child DB Schema Needs Mid-Dive Changes
If the user realizes they need an additional property mid-session: "Add a 'Timeline' column to this analysis." The agent uses MCP to add the property to the child DB and backfills existing items if possible.

---

## Implementation Sequence

### Step 1: Schema Migration (MCP operations)
```
1. notion-update-data-source on Tickets:
   UPDATE "Type" SELECT — add "Dive" option to existing values
```

### Step 2: System Prompt Update
- Add Granular Dives section after Capacity Planning
- Add dive activation to Mode Detection
- Add dive workflow to Standard Workflows
- Update Migration Registry with v1.2 → v1.3 entry
- Update version comment to v1.3

### Step 3: Config Template Update
- Add `dives` top-level section with template configs and settings
- Add "Dive" to tickets.enums.Type
- Add `granular_dive` to workflows
- Update version to 1.3, changelog

### Step 4: Agent Patterns Update
- Add Granular Dives section with patterns for each template type
- Add resume and quick dive patterns

### Step 5: Testing
- Test each template end-to-end: trigger → create → populate → work → synthesize → close
- Test resume across conversations
- Test proactive suggestions during ticket creation and triage
- Test dive + effort tracking integration
- Test spawning tasks from dive outputs (project plan, audit)
- Test mid-dive schema changes
- Test multiple active dives
