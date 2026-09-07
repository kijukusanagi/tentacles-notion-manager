<!-- Tentacles build record — v1.4 hardening pass (Sept 2026).
     Decision log: Phase A approval and every gated diff review (B1, B2, …) with the changes requested and what was accepted as-is.
     One-time record for v1.4; the starting point for v1.5 scoping. Append-only during the pass. -->

# v1.4-hardening — decision log

## Phase A approval (2026-09-07)

Approved all 32 items + #33. Calls:
- #9 Reword existing config-trust rules: "in the config, verified against live schema."
- #21 Fix registry `optional_fields` → `other_properties`. No "historical" annotation.
- #24 Move project-plan.md → docs/history/project-plan-v1.0.md, one-line header, no rewrite.
- #26 Collapse: keep docs/agent-patterns.md, delete examples/agent-patterns.md, update refs.
Modifications:
- #13 Onboarding discovers 8 core by name only; never reads extensions.databases. Extensions added post-onboarding; picked up by doctor + health checks.
- #4 Warn-once on unknown version; forks add their string to the known list in their fork, don't suppress.
- #5 Triggers "doctor" / "config doctor" only. "Health check" stays with alerts. One-line distinction in both sections.
- #22/#23 Every "5 minutes" → "15–30 minutes". No tighter number.
- #30 Registry v1.3→v1.4: add tentacles_config: true, add extensions: {"databases": []}, bump versions. No schema.
Addition:
- #33 Onboarding "Update Documentation Pages" opt-in/deferred; closes with "say 'update docs'". Add "update docs" as Ops trigger running existing Step 2 doc logic. Risk Y.
Process: commits per B-section; prompt diff reviews batched B1, B2, B3, B4/B5. Fresh-setup test script with handoff. Baseline timing run in parallel; friction log → follow-up commit on SETUP/README.

## B1 review — approved with 3 changes
1. Doctor relations: target DB key = first token before space/paren; parenthetical descriptive only.
2. Doctor: live-only properties/options = INFO, not drift. Drift = config-says-but-live-lacks (+ unreachable).
3. Placeholder check extended to project_codes (incl. Project Code enum) and users; tokens inside strings (`{PREFIX}-OPS`) count.
Accepted as-is: Version Check at v1.4 ahead of B5; extensions.databases forward ref; regen preserves extensions metadata.

## B2 review — approved with 3 changes
1. Keep Rule 10 separate from Rule 9. Grep confirmed no positional Core Rule references elsewhere shifted (rules 1–8 unchanged; 9/10 appended).
2. Add Rule 9 reference to Onboarding Step 3 Learn by Doing (ticket + task create) — warm-cache argument fails on the migration path.
3. "in the current session" → "in this conversation" everywhere in Rule 9 and its invalidation clause; also Migration Phase 4.
Clarified: a `doctor` run populates the Rule 9 cache (same read); stated in Rule 9 and in Doctor step 1.
Accepted as-is: new "Adding Project Codes" subsection (ops-mode path previously specified nowhere); Migration Phase 4 target-schema fetch; L69/L177/L470 reference Rule 10 rather than restate it.
Process: B3 and B4/B5 may be one combined review if diffs stay clean. docs/process/ to hold audit prompt, reflection protocol, phase-a-audit, decisions.
