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

## Import task — approved with 2 additions
> "Import approved. Two additions before it commits:
> 1. Add scratchpad/ to .gitignore in the same commit. Those files were never tracked — this is their first entry into version control, not a move. decisions.md in particular is only useful if it's append-only from here.
> 2. In docs/process/README.md, note that decisions.md is append-only: every gated approval block gets appended verbatim, nothing rewritten or summarized."

## Non-gated docs (B4) — approved with 4 changes
> "Non-gated docs approved with four changes. B3's prompt/config-template diffs and B5's registry block are still not reviewed — send those before committing anything in those two sections.
> 1. extensions._example_entry: strip it at config generation time (Onboarding Step 4) and have Startup Config Validation flag it as a placeholder if found in a file marked tentacles_config: true. As written, a generated config can carry {MEETINGS_*} through validation.
> 2. Drop the doctor line from Mode Detection. Triggers stay where the section is defined.
> 3. Keep SETUP's plan wording, and add one sentence: "If you don't see Projects in your sidebar, it may not be available on your plan."
> 4. Add .gitattributes with `* text=auto` in the B4 commit.
> Commit order approved: B3, #33, B4, B5 — but B3 and B5 only after I see their gated diffs."

## B3 + B5 — approved with 2 amendments
> "B3 and B5 approved. Two amendments, then commit.
> 1. Bootstrap sentence — apply it, but tightened. As proposed, "a JSON file with system_prompt_version and a databases map" matches config-template.json and sample-config.json, which is exactly what B1 excludes. Use this shape instead:
> If no file carries "tentacles_config": true, but a JSON file in Project Knowledge has a system_prompt_version and a databases map, treat it as a possible pre-v1.4 config: run Startup Config Validation on it first. If it fails the placeholder check, it is a template, not a config — ignore it and proceed to Onboarding Mode as normal. If it passes, enter Operations Mode; the Version Check will offer the v1.3 → v1.4 migration, which adds the marker. This bootstrap path exists only to reach that migration — once the marker is present, normal Mode Detection applies.
> Put it in Mode Detection with a cross-reference from the registry block. Add the same note to UPGRADING.md so the v1.3 → v1.4 section explains why the first conversation looks different.
> 2. .gitattributes: conservative plan approved. Five modified files convert in B4, leave the six untouched, no renormalize commit.
> Commit B3 → #33 → B4 → B5, append every approval block verbatim to decisions.md, deliver the handoff with the fresh-setup test script.
> Do not open the PR or tag. The prompt is 80,335 chars against an untested Instructions limit — the fresh-setup run resolves that before anything merges. Flag it explicitly in the handoff as the one blocking unknown."
Notes: B3-only doc files (workflows, agent-patterns, troubleshooting "8" sites) ride in B3; docs with mixed B3/B4 edits (README, SETUP, architecture, v1.2 spec) ride in B4. Prompt timing lines (#22/23) ride in B4.

## Post-handoff — generalization pass, approved
> "yes go for it!" (in reply to: swap the fork-specific `cl-1.0` example for a neutral one in the prompt's Startup Config Validation and in UPGRADING.md; leave the pre-existing "Quipos"/"OS Layer Next Effect" migration examples and the test-script template split for after the timed run.)
Rule applied: docs/process/ is the build record and may name forks; everything outside it must read as if the forks don't exist.

## 2026-09-07 — prompt size figures reconciled
The "80,335 chars" in the B3+B5 approval block above is left as quoted. Note for readers: it
matches no committed state of `agent/system-prompt.md` on this branch as either a byte or a
character count — the file goes 78,215 bytes / 76,532 chars at 8989d94 straight to 81,259 /
79,556 at 93d84c2, so 80,335 was most likely measured on an uncommitted working tree between
those two points. Provenance unconfirmed; recorded here rather than corrected in place.
Final for the v1.4 fresh-setup run: **81,263 bytes / 79,560 characters** (`wc -m`), at 2d6c18f.
Characters are what count against the Project Instructions limit, so 79,560 is the operative
figure — roughly 1,700 below the byte count that earlier notes quoted.

## Negative-path test run (checkpoints 0, 14–16) — results and 2 prompt additions
> "Checkpoint 0: PASS — 79,560 chars accepted in Project Instructions
> Test 14: PASS — template with marker false silently ignored, Operations Mode entered
> Test 15: PASS — two marked configs, stopped and asked, no Notion call
> Test 16: PASS — hand-flipped template refused, placeholders named
> Not run: checkpoints 1-13 (happy path), 17 (bootstrap). Timed setup run deferred."
Additions (prompt-only, no schema change), approved:
1. Startup Config Validation check 4 — UUID-shape check on database_id / data_source / teamspace_id: warn once and continue, do not refuse. Gap found by the v1.4 agent itself during testing (fake-but-not-placeholder IDs pass check 1).
2. Single-config and placeholder refusals lead with the action (remove file from Files → new conversation / run onboarding), explanation second.
Blocking unknown (Instructions length) is resolved by checkpoint 0. Tag v1.4 to follow.

## Release gate
> "re-paste passed" — 80,490 characters accepted in Project Instructions. Approved: PR v1.4-hardening → main, squash-merge, annotated tag v1.4 on main at the merge commit (no prior tags exist; format chosen to match prompt header and commit prefixes), push tag, delete remote branch, keep local.
