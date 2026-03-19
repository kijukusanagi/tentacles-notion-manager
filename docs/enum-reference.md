# Enum Reference

The agent requires **exact string matches** for all select fields. Invalid values fail silently in the Notion API — the field just won't be set, with no error returned. Always use the values listed here.

---

## 🎫 Tickets

| Property | Valid Values |
|----------|-------------|
| Status | New, Triaged, In Progress, Blocked, Done, Closed |
| Priority | P0, P1, P2, P3 |
| Source | Human, Agent, Email, Slack |
| Project Code | *(Set during onboarding — varies per workspace)* |

---

## ✅ Tasks

| Property | Valid Values |
|----------|-------------|
| Status | Backlog, To Do, In Progress, In Review, Blocked, Done |
| Priority | Urgent, High, Medium, Low |
| Sprint | Backlog, Sprint 1, Sprint 2, Sprint 3, Sprint 4 |
| Task Type | Task, Bug, Milestone, Spike, Admin |
| Effort Estimate | XS, S, M, L, XL |

---

## 📁 Engagements

| Property | Valid Values |
|----------|-------------|
| Status | Lead, Proposal, Active, On Hold, Completed, Lost |
| Engagement Type | Strategy, Implementation, Advisory, Retainer |

---

## 🚀 Initiatives

| Property | Valid Values |
|----------|-------------|
| Status | Idea, Under Evaluation, Approved, In Progress, Completed, Parked, Rejected |
| Category | Business Development, Internal Capability, Client Expansion, Content/Positioning, Product Development, Partnership |
| Initiative Type | Revenue Generating, Cost Saving, Capability Building, Strategic Positioning |
| Time Horizon | Quick Win < 1 month, Short Term 1-3 months, Medium Term 3-6 months, Long Term 6+ months |
| Impact | 3 = Massive, 2 = High, 1 = Medium, 0.5 = Low, 0.25 = Minimal |
| Confidence | 100% = High, 80% = Medium, 50% = Low |
| Source | Team Idea, Client Request, Market Opportunity, Partner Suggestion, Leadership Priority |

---

## 🧩 Internal Projects

| Property | Valid Values |
|----------|-------------|
| Status | Not Started, Active, On Hold, Completed, Archived |
| Project Type | Product Venture, Operations, Infrastructure, Content, Process Improvement |
| Priority | High, Medium, Low |

---

## 💼 Clients

| Property | Valid Values |
|----------|-------------|
| Status | New Lead, In Discussion, Proposal Sent, Negotiation, Closed Won, Closed Lost |
| Source | Referral, Website, Cold Call, Event, Social Media, Email Campaign, Partner, Direct Inquiry |
| Probability | High (80-100%), Medium (40-79%), Low (0-39%), Uncertain |

---

## 🤝 Partnerships

| Property | Valid Values |
|----------|-------------|
| Partnership Stage | Prospect, In Conversation, Pilot, Active, Dormant, Closed |
| Partnership Type | Referral, Co-selling, Co-delivery, Technology, Marketing, Other |

---

## 📊 OKRs

| Property | Valid Values |
|----------|-------------|
| Status | On Track, At Risk, Behind, Deprioritized, Completed |
| Type | Objective, Key Result |
| Level | Company, Team, Individual |
| Time Period | Q1 2025, Q2 2025, Q3 2025, Q4 2025, Annual 2025, Q1 2026, Q2 2026, Q3 2026, Q4 2026, Annual 2026 |
