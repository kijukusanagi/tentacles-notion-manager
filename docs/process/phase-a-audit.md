<!-- Tentacles build record — v1.4 hardening pass (Sept 2026).
     The Phase A audit report delivered against v1.3 (commit 5fe6c97): inventory, mode-detection/config-trust findings,
     live-schema drift ratio, hardcoded-count sites, knowledge-kit classification, onboarding friction, and the 32-item change list.
     One-time record — findings are specific to v1.3; the structure is the reusable part (see v1.4-audit-prompt.md). -->

# Tentacles v1.4 — Phase A Audit Report

Repo: `kijukusanagi/tentacles-notion-manager` @ `main` (5fe6c97), v1.3. Read-only pass; no edits made.

Labels: **VERIFIED** = read in repo · **ASSUMED** = inferred · **UNKNOWN** = cannot determine from repo.

---

## A1. Inventory and coherence

### Inventory

| File | Lines | Self-declared version (exact line) | Actually reflects |
|---|---|---|---|
| `agent/system-prompt.md` | 1325 | L1 `<!-- TENTACLES SYSTEM PROMPT v1.3 — ... -->`; L7 `This system prompt is **v1.3**.` | v1.3 — VERIFIED |
| `agent/config-template.json` | 693 | L2 `"version": "1.3"`, L3 `"system_prompt_version": "1.3"` | v1.3 — VERIFIED |
| `examples/sample-config.json` | 242 | L2 `"version": "1.0"`; no `system_prompt_version` key | v1.0 — VERIFIED. Missing `system_prompt_version`, `migrations`, `effort`, `alerts`, `capacity`, `dives`; Tickets has no `Type` enum; Tasks has no Hours fields; carries a `project_code_descriptions` key that the v1.3 template does not have. |
| `CHANGELOG.md` | 42 | L5 `## [1.3] - 2026-03-19` (top entry) | v1.3 — VERIFIED |
| `UPGRADING.md` | 68 | none (top section is L51 `## Upgrading from v1.2.1 to v1.3`) | v1.3 — VERIFIED, but see hop coverage below |
| `README.md` | 208 | none | v1.3 features listed (L31–34) — VERIFIED; but L62 references `<tentacles_operating_system>` tags that do not exist in the prompt (grep: 0 hits) — VERIFIED stale. Project structure L144 lists `brand/09-partnership-whale.png`, which is not on disk — VERIFIED. |
| `SETUP.md` | 67 | none | v1.3 (mentions dives L55) — VERIFIED; L5 links to literal `TEMPLATE_URL` (broken) — VERIFIED |
| `docs/architecture.md` | 179 | none | v1.0/1.1 — VERIFIED: L140 "5 standard workflows" (template has 12); L158 child DBs "must include a Source Ticket relation" contradicts config `conventions.dive_child_db_note`; no mention of Type/Hours/effort/alerts/capacity/dives. |
| `docs/project-plan.md` | 155 | none; roadmap checkboxes stop at v1.1 (L143–149) | v1.0/v1.1 planning doc — VERIFIED. See coherence below. |
| `docs/granular-dives-spec.md` | 754 | L1 `# Tentacles v1.3 — Granular Dives` | v1.3 design spec — VERIFIED; registry label mismatch (below). |
| `docs/v1.2-release-spec.md` | 740 | L1 `# Tentacles v1.2 — Tier 1: Agent → Operator` | v1.2 design spec — VERIFIED; schema blocks superseded (below). |
| `docs/migration.md` | 600 | none; roadmap L580–599 "v1.1 Core / v1.2 Incremental Sync / v1.3 Advanced" | v1.1 spec. Its roadmap versions don't correspond to what v1.2/v1.3 actually shipped (effort/alerts/dives) — VERIFIED. Prompt already includes incremental sync (L577–603), which this doc's roadmap places in "v1.2". |
| `docs/enum-reference.md` | 89 | none | v1.0/1.1 — VERIFIED: Tickets table lacks `Type` (Request/Bug/Decision/Alert/Proposal/Dive); README L176 calls it "complete". |
| `docs/workflows.md` | 98 | none | v1.0 ("five standard workflows" L3) — VERIFIED stale vs 12 in config. |
| `docs/project-codes.md` | 58 | none | Version-neutral — VERIFIED. L58 "agent validates against the config's code list" reinforces config-as-authority (A3). |
| `docs/troubleshooting.md` | 95 | none | v1.0/1.1 — VERIFIED. References `*-config-*.json` glob (L61, L93) and UI paths "Settings → Integrations", "Settings → Project Knowledge" that differ from SETUP's "⚙️ → Files". |
| `docs/agent-patterns.md` | 313 | section headers `(v1.2)`, `(v1.3)` | v1.3 — VERIFIED |
| `examples/agent-patterns.md` | 313 | same | Byte-identical to `docs/agent-patterns.md` (`diff` clean) — VERIFIED |
| `examples/sample-prompts.md` | 149 | none | v1.0 — VERIFIED: no effort/alert/capacity/dive prompts. |
| `notion-template/TEMPLATE_LINK.md` | 24 | none | Untouched per ground rules. |
| `brand/README.md` | 42 | none | Untouched per ground rules. |
| `LICENSE` | — | — | Untouched. |

### Confirm / refute

| Claim | Result |
|---|---|
| `sample-config.json` declares v1.0 while template is v1.3 | **CONFIRMED** — VERIFIED (`examples/sample-config.json:2` vs `agent/config-template.json:2`). |
| `project-plan.md` describes 8-DB system with a 3-step SETUP; `SETUP.md` has 6 steps | **CONFIRMED** — VERIFIED: `docs/project-plan.md:15` "SETUP.md — Detailed setup guide (3 steps)", `:139` "SETUP.md (3 steps: duplicate template, create Claude Project, paste prompt)"; `SETUP.md` has Steps 1–6. Also lists `agent/system-prompt-template.json` (L23) which does not exist, and calls the generated file `config.json` (L48, L87, L124) — a name that does **not** match the prompt's `*-config-*.json` glob. |
| `granular-dives-spec.md` labels migration `v1.2 → v1.3`; prompt registry says `v1.2.1 → v1.3` | **CONFIRMED** — VERIFIED: `docs/granular-dives-spec.md:426` and `:734` vs `agent/system-prompt.md:57`. Spec's Steps block also has 4 steps (omits "Add granular_dive to workflows") vs 5 in the prompt. |
| `v1.2-release-spec.md` publishes Tickets `Type` enum without `Dive` and puts `Hours Spent`/`Hours Estimated` under `optional_fields` | **CONFIRMED** — VERIFIED: `docs/v1.2-release-spec.md:659` `"Type": ["Request","Bug","Decision","Alert","Proposal"]`; `:640-645` `optional_fields: ["Hours Spent","Hours Estimated",...]` plus a `number_properties` block. The v1.3 template has `Type` incl. `Dive`, puts the Hours fields under `other_properties`, has `optional_fields: []` on Tasks, and has no `number_properties` key. The prompt's own registry (`system-prompt.md:38`) also says `optional_fields: add "Hours Spent","Hours Estimated"` — so the registry text disagrees with the template it ships with. |
| CHANGELOG and UPGRADING cover every hop 1.0→1.1→1.2→1.2.1→1.3 | **CHANGELOG: CONFIRMED** (entries for 1.0, 1.1, 1.2, 1.2.1, 1.3 — all dated 2026-03-19). **UPGRADING: REFUTED** — VERIFIED: sections exist for 1.1→1.2, 1.2→1.2.1, 1.2.1→1.3; **no `v1.0 → v1.1` section** even though the prompt registry has one. |

Additional coherence findings (VERIFIED):
- `README.md:189` "A Claude Pro or Team account (required for Projects and the Notion MCP integration)" vs `SETUP.md:21` "Connectors are available on all Claude plans, including Free." Contradiction.
- `README.md:99–111` tells users to upload 5 files to Project Knowledge; `SETUP.md` Step 6 tells them to upload 1. See A5.
- `agent/system-prompt.md:1` header says "The agent uses it for version checks" but the version check (L636–642) reads `system_prompt_version` from the config and compares to "this prompt's version (v1.3)" in prose — the HTML comment is not otherwise referenced. Cosmetic, but note both L1 and L7 and L273 and L638 all need bumping in B5.

---

## A2. Mode detection and config trust

Source: `agent/system-prompt.md:81–89` (Mode Detection), `:634–642` (Startup: Version Check), `:272–279` (Step 4: Generate Config).

**1. Exact file-matching rule.** L83: "Check Project Knowledge for a config file (any file matching `*-config-*.json`)." — VERIFIED.
- `config-template.json` — under strict glob semantics, **no**: the pattern requires a literal `-config-` substring and the filename starts with `config-` (no leading hyphen). ASSUMED: an LLM applying this rule fuzzily may well treat it as a match, and README L108 explicitly tells users to upload this very file to Project Knowledge, so the collision is realistic in production.
- `tentacles-config-template-v1_3.json` — **yes**, strict match (`tentacles` + `-config-` + `template-v1_3` + `.json`) — VERIFIED by glob semantics. A renamed template would put the agent into Operations Mode on placeholder IDs.
- The prompt never tells the agent what filename to give the generated config (grep for filename guidance: only L83, L279, L632 — none name the file). `docs/troubleshooting.md:67` documents the resulting failure ("rename it to something like `acme-config-v1.json`") — VERIFIED. `docs/project-plan.md` calls it `config.json` throughout, which would not match.

**2. Placeholder check before Operations Mode?** **None.** VERIFIED — Operations Mode startup (L632–642) does only the version compare. No instruction anywhere checks `_id` / `data_source` values against `{PLACEHOLDER}` form. 24 distinct placeholders exist in the template (`{TICKETS_DB_ID}` … `{CLIENT_CODES}`, `{DATE}`, `{PREFIX}`×11).

**3. Check that config IDs correspond to fetchable databases?** **None.** VERIFIED — the only live-fetch verification is in Onboarding Step 1 (L110–121) which discovers by name, not by config ID. Operations Mode trusts the config's IDs on every call (L651 "Reference the config file for exact database IDs and data source IDs").

**4. Two files match?** **Unspecified.** VERIFIED — the rule is "if a config file exists"; no tie-break, no "ask the user", no "newest wins".

**5. `system_prompt_version: "2.1"`?** The rule is a three-way prose comparison (match / older / newer) against "this prompt's version (v1.3)" with a fallback "missing → treat as v1.0". `"2.1"` would be judged "newer" and produce the "update your system prompt" warning — ASSUMED (LLM-evaluated string comparison). There is **no known-version list** in the prompt; a non-numeric or malformed value (`"1.3-cl"`, `"latest"`, `"1.2.5"`) has no defined handling — VERIFIED absence.

---

## A3. Live-schema drift

### Places the prompt tells the agent to trust the config for schema (VERIFIED)

| Loc | Text | What is trusted |
|---|---|---|
| L632 | "Load the config… This contains all database IDs, data source IDs, enum values, relation maps, and conventions." | IDs, enums, relations |
| L651 | "Reference the config file for exact database IDs and data source IDs" | IDs |
| L669–678 | Core Rule 3: "Always reference the config for valid values" + **hardcoded** Status lists for all 8 DBs | enums (config *and* prompt-inline) |
| L723 | "Project Code (best match from config)" | Project Code enum |
| L754–756 | Hours mapping from `effort.hours_mapping` | settings (fine) |
| L1308 | "Never use enum values not in the config." | enums |
| L1316 | "Never guess relation property names — check the config." | relation property names |
| L110–118, L649–659 | Enumerated 8-DB list by name | DB count/names |
| `docs/project-codes.md:58` | "The agent validates against the config's code list" | Project Code enum |
| `docs/enum-reference.md:3` | "Always use the values listed here." | enums (doc, static) |

### Places it tells the agent to fetch live schema first (VERIFIED)

| Loc | Text | Scope |
|---|---|---|
| L120 | Onboarding Step 1.5: "Quick-check the Tickets database schema — verify it has relation properties…" | Onboarding only; Tickets only; relations only |
| L353 | Migration Phase 1: "fetches the schema via `notion-fetch`… property names, types, select/multi-select options, relations" | **Source** databases (foreign), not Tentacles targets |
| L1173 | Dives: "Mid-dive schema changes… use MCP to add the property" | Write, not a read-before-write |

**Ratio:** in Operations Mode write paths, **config-trust : live-fetch = ~7 : 0**. Across the whole prompt, ~10 : 2, and both fetches are outside Operations Mode write paths.

### Write paths with no live-schema guard (VERIFIED, Operations Mode)

1. Smart Ticket Creation (L690–735) — sets Project Code, Priority, Type, Status, Source, relations to Clients/Engagements/Initiatives/Projects.
2. Task creation (L740) — Status, Priority, Sprint, Effort Estimate, Hours, Source Ticket + 5 other relations.
3. Per-Database Operations (L739–746) — status/type selects on Engagements, Initiatives, Internal Projects, Clients, Partnerships, OKRs.
4. Alert ticket creation (L843–852, L889–899) — Type = Alert, Project Code = `{PREFIX}-OPS`.
5. Migration Phase 4 execution into Tentacles targets (L488–497) — "enum exact matches" against config, no target fetch. (Source schema *is* fetched; target is not.)
6. Migration Project Code Generation (L433–437) — "Add approved codes to the Tickets database enum via MCP" — no instruction to re-declare existing options. **Select-option-drop hazard.**
7. Dive start (L1119–1126) — sets Type on the parent ticket; child-DB create is safe (new DB).
8. Migration Registry steps (L45–46, L69) — `update-data-source` column adds/alters run without a prior fetch; L69 does say "preserve existing values" for Type (the corollary exists here only, and only for one column).
9. Onboarding Apply Configuration (L174–178) — `ALTER COLUMN SET SELECT(...)` for Project Code with "Include any existing codes" (L175 — the corollary, informally); then **`ALTER COLUMN ADD "Type"`** and **`ADD COLUMN "Hours Spent"`** — if the v1.3 hosted template already ships these columns (ASSUMED likely, since the registry treats them as v1.2/v1.3 schema and the template is v1.3), this is a duplicate-add against live schema with no pre-check. UNKNOWN what the MCP does on duplicate ADD.

Ops-mode "add a project code" (advertised in `docs/project-codes.md:55`, `examples/sample-prompts.md:136`) has **no corresponding instruction in the prompt at all** — grep for ops-mode project-code addition returns only the onboarding and migration lines. VERIFIED gap.

---

## A4. Hardcoded database count (VERIFIED, full grep)

Patterns: `8 database(s)`, `8 DBs`, `all 8`, `eight`, `8 interconnected`, `8-database`, plus enumerated name lists.

| File | Lines | Form |
|---|---|---|
| `agent/system-prompt.md` | L3 ("8 interconnected databases"), L110 ("all 8 databases" + enumerated list L111–118), L119 ("all 8 databases"), L121 ("Found all 8 databases"), L269 ("across all 8 databases"), L649 (`## The 8 Databases` + enumerated list L651–659), L661 ("Every database can reach every other within 1-2 hops") | 6 numeric + 2 enumerated lists. Also implicit: L203–214 placeholder list (8 DB ID pairs), L306–314 migration dependency order (8 named), L369–378 routing table (8 targets), L672–678 status lists (8 named). |
| `agent/config-template.json` | `databases` map has exactly 8 keys; `cross_database_relations` 8 keys; no count literal | Structural, not literal — extensible as-is. |
| `README.md` | L9, L18, L28, L42, L84 (`## The 8 Databases` + table), L110, L176 | 7 |
| `SETUP.md` | L5, L54 | 2 |
| `CHANGELOG.md` | L38 (historical, v1.0 entry — leave) | 1 |
| `docs/architecture.md` | L5 (×2), L9, L116, L134 | 5 |
| `docs/workflows.md` | L93, L98 | 2 |
| `docs/troubleshooting.md` | L7 | 1 |
| `docs/agent-patterns.md` / `examples/agent-patterns.md` | L193 (each) | 2 |
| `docs/migration.md` | L84 (example output — fine) | 1 |
| `docs/project-plan.md` | L5, L28, L59, L116 | 4 |
| `docs/v1.2-release-spec.md` | L160 | 1 |
| `notion-template/TEMPLATE_LINK.md` | L9 | do-not-touch |
| `docs/enum-reference.md` | 8 sections by name | enumerated |

No `eight` (word) or `8 DBs` hits. Total literal/enumerated sites to patch in-scope: ~35 across 11 files (excluding CHANGELOG history and TEMPLATE_LINK).

---

## A5. Project-knowledge kit vs repo docs

| File | Class | Note |
|---|---|---|
| `agent/system-prompt.md` | **Operating kit** (Instructions, or Files per SETUP L30) | — |
| generated `<name>-config-<x>.json` | **Operating kit** | The mode switch. |
| `docs/agent-patterns.md` ≡ `examples/agent-patterns.md` | **Operating kit** | Identical duplicates — pick one canonical location. |
| `docs/enum-reference.md` | **Both / unclear** | Useful at runtime but currently stale (no Type); as project knowledge it would contradict the config. Also redundant with config enums; and the v1.4 live-schema rule makes it advisory only. |
| `docs/migration.md` | **Both / unclear** | README says upload. Prompt already contains Migration Mode in full; the doc's roadmap (L580–599) mislabels versions. Marginal value, low risk. |
| `agent/config-template.json` | **Both / unclear → should be Repo doc** | README L108 says upload it. It carries 24 placeholders and near-matches the config glob. **Highest-risk misleading item.** |
| `docs/architecture.md` | **Both / unclear → Repo doc** | README L110 says upload. Stale: "5 workflows", child-DB Source Ticket rule contradicts dive convention. |
| `docs/v1.2-release-spec.md` | **Both / unclear → Repo doc** | README L111 says upload. Contains superseded schema blocks (Type without Dive; Hours under optional_fields). **Could mislead the agent** into treating `Dive` as invalid or regenerating a config in the wrong shape. |
| `docs/granular-dives-spec.md` | **Repo doc** | Not recommended for upload; has the mislabeled registry entry. |
| `README.md`, `SETUP.md`, `UPGRADING.md`, `CHANGELOG.md`, `LICENSE` | **Repo doc** | — |
| `docs/project-plan.md` | **Repo doc (historical)** | Wrong about SETUP steps, file names, config name. |
| `docs/troubleshooting.md`, `docs/project-codes.md`, `docs/workflows.md` | **Repo doc** | Human-facing. |
| `examples/sample-config.json` | **Repo doc** | Fake IDs (`ghij-klmn…` non-hex); v1.0 shape. Must never be in project knowledge; filename `sample-config.json` doesn't strictly match the glob but is fuzzy-close. |
| `examples/sample-prompts.md` | **Repo doc** | Human-facing. |
| `notion-template/`, `brand/` | **Repo doc** | Untouched. |

**Does SETUP's upload list match?** No — VERIFIED. `SETUP.md` Step 6 lists only `examples/agent-patterns.md`. `README.md` L105–111 lists five: agent-patterns, **config-template.json**, migration.md, architecture.md, **v1.2-release-spec.md**. Two of README's five are misleading-in-knowledge per the classification above; SETUP and README disagree with each other.

---

## A6. Onboarding friction

Walking `SETUP.md` → prompt Onboarding Mode (L91–279) as a first-time user.

### Steps requiring knowledge the docs don't provide
1. `SETUP.md:5` link target is the literal string `TEMPLATE_URL` — user must find the real link in README L42 or TEMPLATE_LINK.md. VERIFIED.
2. `README.md:62` "including the `<tentacles_operating_system>` tags" — no such tags exist; user will hunt for them. VERIFIED.
3. Instructions field vs Files: the prompt is 66,327 chars. Whether claude.ai Project Instructions accept that length is UNKNOWN; SETUP L30 offers the Files alternative without saying when it's needed. ASSUMED some users hit a limit.
4. Saving the generated config: the agent "presents the file for download" (L277) but nothing tells the user or the agent the filename must match `*-config-*.json`. Only `docs/troubleshooting.md:67` mentions it. VERIFIED.
5. "Start a new conversation after uploading" appears only in SETUP's troubleshooting (L65), not in Step 5. VERIFIED.
6. Which files to upload as knowledge — SETUP and README disagree (A5).
7. Requirements: README says Pro/Team required; SETUP says Free works. User can't tell. VERIFIED.
8. UI path names differ: SETUP "⚙️ gear → Files"; troubleshooting "Settings → Project Knowledge" / "Settings → Integrations". VERIFIED.
9. Teamspace selection: prompt L109 assumes the template lives in a teamspace and asks the user to pick one if multiple "OS Layer" pages exist; a user who duplicated into a personal/private space may not know what a teamspace is. ASSUMED.

### Steps that could silently fail
1. Config uploaded under a non-matching name → agent loops into onboarding forever (troubleshooting L89–95 confirms this is a known symptom). VERIFIED.
2. `config-template.json` uploaded per README L108 → agent may treat it as the config and enter Operations Mode on placeholders, or skip onboarding. ASSUMED (see A2.1).
3. Onboarding Apply Configuration L176–177: `ALTER COLUMN ADD "Type"` and `ADD COLUMN "Hours Spent"/"Hours Estimated"` on a v1.3 template that (ASSUMED) already has them. Outcome UNKNOWN — duplicate property, error, or silent no-op. Either way the confirmation message L178 fires regardless.
4. L174 `ALTER COLUMN SET SELECT(...)` on Project Code — if the agent forgets L175 "include any existing codes", pre-existing options are dropped. Select-drop hazard.
5. Enum writes fail silently by design (L669 "Invalid values fail silently") — any enum drift from the config is invisible.
6. Documentation-page update (L184–229): four pages, full rewrites via `replace_content`/`update_content`. If a page title was changed by the user or duplication, the search returns nothing and the step is skipped; the confirmation L229 still fires. ASSUMED.
7. `get-users` (L143) may return guests/bots; no filtering instruction. ASSUMED.
8. Step 1.4 cross-teamspace verification: "If any database's data source URL points to a different teamspace… stop" — the agent has no reliable way to derive teamspace from a data source URL. UNKNOWN whether MCP exposes this.

### "5 minutes" plausibility
Promised in `SETUP.md:1`, `:36`, `README.md:9/30/68/190`, prompt L95/L99, `project-plan.md:56`. Onboarding as written does: 8+ DB fetches; 4 personalization Q&A rounds (identity, team, ~10 project codes review, clients, effort/capacity defaults); 4 MCP schema writes; **4 documentation-page fetch-and-rewrite operations** (dozens of placeholder replacements each); an optional migration branch; 2 record creates with confirm loops; and generation of a ~700-line JSON the user must copy, save with the correct name, upload, and then start a new chat. ASSUMED realistic: 15–30 minutes for a first-timer, dominated by the doc-page rewrite and the config-save round trip. The per-step timings in `project-plan.md:56–91` (1+2+2+0.5 min) omit the doc-page step entirely — VERIFIED.

## Friction log (external)

_Placeholder — merge the timed fresh-setup friction log here._

---

## Proposed v1.4 change list

Risk column = does it alter agent behavior? (Y/N). Items marked ⛔ are gated per ground rules (diff review before applying).

### B1 — Config trust hardening
| # | File(s) | Change | Risk |
|---|---|---|---|
| 1 | ⛔ `agent/system-prompt.md` L83 | Replace `*-config-*.json` glob with "a JSON file in Project Knowledge whose top-level `tentacles_config` is `true`"; state explicitly that files with `false`/absent key are templates and must be ignored for mode detection | **Y** |
| 2 | ⛔ `agent/config-template.json`, `examples/sample-config.json` | Add `"tentacles_config": false` + `"_comment"` explaining marker | N (template) |
| 3 | ⛔ `agent/system-prompt.md` L277–279 (Step 4) | Tell the agent to emit `"tentacles_config": true` in generated configs and to suggest a filename | **Y** |
| 4 | ⛔ `agent/system-prompt.md` L634 (before Version Check) | New "Startup: Config Validation": placeholder regex `^\{[A-Z_]+\}$` on every `database_id`/`data_source`/`teamspace_id` → refuse; >1 marked file → stop and ask; `system_prompt_version` not in known list `["1.0","1.1","1.2","1.2.1","1.3","1.4"]` → warn once, name list; missing → v1.0 (keep) | **Y** |
| 5 | ⛔ `agent/system-prompt.md` (new subsection under Operations Mode) | `doctor` behavior: on "doctor" / "check config" / "health check config" — fetch each DB in `databases` (+ `extensions.databases`) by data source, diff properties/enums/relation targets vs config, print drift table, offer regenerated config. Read-only. Distinguish from existing "health check" (alerts) trigger — add disambiguation line | **Y** |
| 6 | `docs/troubleshooting.md` L57–67, L89–95 | Replace glob guidance with marker guidance | N |

### B2 — Live-schema-first
| # | File(s) | Change | Risk |
|---|---|---|---|
| 7 | ⛔ `agent/system-prompt.md` Core Rules (after L678) | New Core Rule 9: before any write that sets a select/status, a relation, or names a property, fetch the live data source schema for that DB once per session; config is a hint, live is authority; on drift, use live and tell the user to run `doctor` | **Y** |
| 8 | ⛔ `agent/system-prompt.md` same rule + L174, L437, L69 | Corollary: when altering a select column, re-declare the full existing option list — Notion drops unmentioned options | **Y** |
| 9 | ⛔ `agent/system-prompt.md` L669–678, L1308, L1316 | Soften "Always reference the config" / "Never use enum values not in the config" / "check the config" to "config, verified against live schema" so they don't contradict Rule 9. Keep the inline status lists (behavioral tuning) but label them "defaults shipped with the base template" | **Y** (wording only) |
| 10 | ⛔ `agent/system-prompt.md` L176–177 | Onboarding Apply Configuration: add "fetch Tickets/Tasks schema first; only ADD Type/Hours if absent" | **Y** |
| 11 | `docs/enum-reference.md` | Add Tickets `Type` row; header note that live schema is authoritative | N |
| 12 | `docs/project-codes.md` L58 | Align with live-schema rule | N |

### B3 — Database extensibility
| # | File(s) | Change | Risk |
|---|---|---|---|
| 13 | ⛔ `agent/system-prompt.md` L3, L110–121, L269, L649–661 | Replace "8" with "the databases listed in the config's `databases` map (the base template ships 8)"; onboarding discovers the 8 core by name, then "any additional databases listed under `extensions.databases`" | **Y** (minor) |
| 14 | ⛔ `agent/config-template.json` | Add `"extensions": { "databases": [] }` with a `_schema` example entry: same shape as core entry + `"role"`, `"relations_to_tickets"` | N |
| 15 | ⛔ `agent/system-prompt.md` (Operations Mode, after "The Databases") | Short "Extension databases" paragraph: what the agent does with them (read/write per entry, include in health checks & doctor), the 2-hop-to-Tickets rule, what it won't do (no auto-routing in migration, no alerts unless configured) | **Y** |
| 16 | `docs/extending.md` (new) | How to add a DB; two-artifact rule (repo/config vs Notion template); agent will/won't; note Meetings + Suggested Tickets as v1.5 candidates | N |
| 17 | `README.md`, `SETUP.md`, `docs/architecture.md`, `docs/workflows.md`, `docs/troubleshooting.md`, `docs/agent-patterns.md`, `examples/agent-patterns.md`, `docs/v1.2-release-spec.md` L160 | Replace hardcoded "8" with "8 core (extensible)" wording; leave CHANGELOG history and TEMPLATE_LINK alone | N |

### B4 — Docs reconciliation
| # | File(s) | Change | Risk |
|---|---|---|---|
| 18 | ⛔ `examples/sample-config.json` | Regenerate to v1.4 shape (all sections, Type incl. Dive, Hours in `other_properties`, `extensions`, marker false, `system_prompt_version`); drop `project_code_descriptions` or keep as documented optional — recommend drop | N |
| 19 | `docs/granular-dives-spec.md` L426, L734 | Relabel `v1.2.1 → v1.3`; add 5th step | N |
| 20 | `docs/v1.2-release-spec.md` (top) | Historical header: schema blocks superseded; point to config-template | N |
| 21 | ⛔ `agent/system-prompt.md` L38 | Registry v1.1→v1.2 text: `optional_fields` → `other_properties` to match the template it ships (historical accuracy; no behavior) | N |
| 22 | `SETUP.md` | Fix `TEMPLATE_URL`; add "what goes in Project Knowledge / what does not" block per A5; add marker + filename + "start a new chat" to Step 5; reconcile plan requirement with README | N |
| 23 | `README.md` | L62 drop `<tentacles_operating_system>`; L99–113 knowledge table → match SETUP (remove config-template & v1.2 spec from upload list); L144 drop missing `09-partnership-whale.png` from tree; L189 reconcile plan requirement | N |
| 24 | `docs/project-plan.md` → `docs/history/project-plan-v1.0.md` | Move with one-line header ("Historical planning doc, v1.0–v1.1; see architecture.md") | N |
| 25 | `docs/architecture.md` | "5 workflows" → current; child-DB Source Ticket note reconciled with dive convention; add `extensions` + marker to Config File section | N |
| 26 | `docs/agent-patterns.md` vs `examples/agent-patterns.md` | Keep both identical (README links both) or make `examples/` a pointer — recommend keep identical, sync in same commit | N |
| 27 | `UPGRADING.md` | Add missing `v1.0 → v1.1` section; add `v1.3 → v1.4` | N |
| 28 | `docs/migration.md` L580–599 | Relabel roadmap tiers (they don't match shipped versions) | N |

### B5 — Version plumbing
| # | File(s) | Change | Risk |
|---|---|---|---|
| 29 | ⛔ `agent/system-prompt.md` L1, L7, L273, L638 | v1.3 → v1.4 | N |
| 30 | ⛔ `agent/system-prompt.md` after L74 | `## v1.3 → v1.4` registry entry: schema none; config add `tentacles_config`, `extensions`; behavioral: validation, live-schema rule, doctor, extensible count | **Y** (migration offer) |
| 31 | ⛔ `agent/config-template.json` L2–5 | `version`, `system_prompt_version` → 1.4; prepend changelog | N |
| 32 | `CHANGELOG.md`, `UPGRADING.md` | v1.4 entries | N |

### Out of scope / flagged, not proposed
- Prompt length vs Instructions limit (UNKNOWN) — note in SETUP only.
- Onboarding "Update Documentation Pages" step is the biggest time sink; not touched in v1.4 (would require template changes to remove placeholders).
- `docs/migration.md` incremental-sync text is already in prompt; no change beyond roadmap labels.
- `brand/README.md` lists `09-partnership-whale.png` (missing) — do-not-touch.
