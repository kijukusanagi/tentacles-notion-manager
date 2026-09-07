<!-- Tentacles build record — v1.4 hardening pass (Sept 2026).
     Self-check protocol (R0–R5) intended to run alongside a Phase A audit and emit reflection/00–05 output files.
     Status: the v1.4 Phase A run did NOT produce the reflection/00–05 files — this protocol is untested as written.
     Scheduled for its first real run on the cl-1.0 → cl-1.1 rebase. Reusable: yes, once validated. -->

# Reflection Protocol — Tentacles Phase A Analysis

This prompt wraps the Phase A audit in `tentacles-v1_4-claude-code-prompt.md`. Run both: that file says *what* to audit; this file says *how to think while doing it*. Where they conflict, this file's process rules win, that file's scope rules win.

Repo: `kijukusanagi/tentacles-notion-manager`, `main`, v1.3. Read-only throughout. No edits, branches, or commits.

## What this repo is

Not code. The artifact under analysis is `agent/system-prompt.md` — 1,325 lines of natural-language behavior specification for a Notion-operating agent — plus its config template and docs. There is no call graph. "Bugs" are behavioral: places where a reasonable agent reading the prompt would do the wrong thing, do nothing, or do two contradictory things. Reflect on *behavior the text produces*, not on the text.

## The rule that governs everything else

**Every reflection step must produce an artifact that changes something downstream.** A finding demoted, a file re-read, a label changed, a hypothesis killed. If a step produces only prose ("I considered whether…"), it did not happen. At the end you will score each checkpoint on whether it moved anything, and the ones that didn't get cut from the next run of this protocol.

---

## R0 — Before reading anything

Write `reflection/00-priors.md`:

1. **Ten hypotheses** about what's wrong with this repo, ranked by expected severity. Write them before opening a single file. Sources you're allowed to draw on: the v1.4 prompt's stated concerns, general knowledge of how prompt-specified agents fail, nothing else.
2. For each: what evidence would **confirm** it, what would **disconfirm** it, and where in the repo you expect that evidence to live.
3. **Reading plan**: the order you'll read files and why. State what you expect to skip and the condition under which you'd un-skip it.
4. **Budget**: how many files, roughly how many lines, before you expect to have enough to write findings. You'll compare against actual.

This file is frozen once written. You will annotate it later; you will not rewrite it.

## R1 — Evidence ledger (continuous)

Maintain `reflection/01-ledger.md` as you read. One row per claim you're inclined to make:

| # | Claim | File:line | Label | Hypothesis it bears on | Direction |
|---|---|---|---|---|---|

- **Label** is VERIFIED / ASSUMED / UNKNOWN. VERIFIED means you can quote the line. ASSUMED means you inferred it from adjacent text. UNKNOWN means you want it to be true and can't find it.
- **Direction** is confirms / disconfirms / neutral, against the R0 hypothesis it bears on. A row with no hypothesis is fine — write "new" — but note it: new rows are where your priors were wrong.
- **Hard rule:** any claim about what the agent *would do* in a situation must cite the prompt text that produces that behavior. If you can't cite it, the label is ASSUMED, no exceptions. This is where prompt analysis goes wrong — you simulate the agent in your head and report the simulation as the spec.

## R2 — Checkpoint after every 3 files

Append to `reflection/02-checkpoints.md`:

1. **Ledger delta**: how many rows since last checkpoint, split by label.
2. **Prior adjustment**: which R0 hypotheses moved, which direction, by how much (use rough probabilities — 0.3 → 0.7 is fine, precision is not the point, the *record of movement* is).
3. **One thing you're avoiding**: a file, a section, or a question you've noticed yourself routing around. Name it. Then either read it now or write the reason you're deferring it. "It's long" is a reason to read it, not defer it.
4. **Reading plan drift**: are you on the R0 plan? If not, was the deviation a decision or a drift?

Cost: this should take under 5% of the time between checkpoints. If it's taking more, the checkpoint is too heavy — say so in the checkpoint itself.

## R3 — Disconfirmation pass (after first full read, before writing findings)

Take your **top five findings** by severity. For each, write `reflection/03-disconfirm.md`:

1. **Steelman the repo.** Write the strongest argument that this isn't actually a problem — the author had a reason, the failure mode doesn't occur in practice, a later section handles it, the config compensates. Then go look for that argument in the repo. Cite what you find or state that you searched and found nothing.
2. **Trace the behavior end-to-end.** For a behavioral finding ("agent enters Operations Mode on a placeholder config"), walk the prompt as the agent would: what section is read first, what decision gets made, what text drives it, what happens next. Write the trace. If the trace breaks — some section you hadn't accounted for intercepts — the finding changes. Record the trace even when it confirms.
3. **Severity re-rate.** Original severity, severity after steelman and trace, one line on what moved it.

Expect at least one of the five to get demoted. If none do, either your findings are unusually solid or your steelmen were weak — reread them and decide which, and write that decision down.

## R4 — Red-team your own report (after draft findings)

Write `reflection/04-redteam.md`. Adopt the stance of the repo author reading your Phase A report with the intent of rejecting it. Answer:

1. Which findings rest on a **single line** that could be read another way? List them. For each, quote the line and the alternative reading.
2. Which findings are actually **the same finding** stated twice with different framing? Merge them.
3. Which findings are **taste** (you'd have written it differently) dressed as defects? Cut or relabel as suggestions.
4. Which finding, if wrong, would most **embarrass** the report? Re-verify that one from scratch — reopen the file, don't trust your ledger row.
5. What did the author get **right** that a naive reviewer would flag as wrong? At least two. If you can't find two in 5,500 lines of a system someone has run in production, you're not reading generously enough, and your findings are probably over-indexed on surface.

Fold the results back into the findings. The report should be shorter after R4, not longer.

## R5 — Calibration and decision log (final)

Write `reflection/05-calibration.md`:

1. **R0 hypotheses, resolved.** For each of the ten: initial rank, final status (confirmed / disconfirmed / unresolved), final severity, and one line on the biggest single piece of evidence. Compute: how many of your top-5 priors ended in your top-5 findings? That number is your calibration score for this run. Write it down without commentary.
2. **Budget vs actual.** Files and lines planned vs read. If you read more than planned, what pulled you in and was it worth it. If less, what did you decide not to need.
3. **New findings** (ledger rows marked "new"): what class of problem were you blind to at R0? One sentence each. This is the list that improves the next R0.
4. **Decision log.** Every point where you chose between two ways of proceeding — reading order, whether to trace a behavior, whether to demote a finding. Format: `decision | alternative | reason | would-reverse-if`. Aim for 8–15. If you have 3, you weren't noticing your decisions; if you have 40, you're logging noise.
5. **Checkpoint audit.** For each of R0–R4: did it change a finding, a label, a severity, or a reading decision? Yes/no and what. Any checkpoint that changed nothing gets marked **RITUAL** and a one-line recommendation: cut it, lighten it, or move it.

## Deliverables

1. The Phase A report as specified in the v1.4 prompt — sections A1–A7 — **with every finding carrying its ledger row number** so the evidence trail is followable.
2. `reflection/00` through `05` as separate files.
3. A closing note, under 200 words, to whoever runs this protocol next: what to keep, what to cut, what you'd add. This is the only place you're allowed to talk about the process in the first person.

Stop. Do not proceed to Phase B.

## What this protocol is not

- Not a license to be slow. R2 checkpoints are capped at 5%. R3 covers five findings, not all of them. The total reflection overhead should be under a third of the analysis time. If it's more, the protocol failed and R5.5 should say so.
- Not a place to hedge. The ledger labels exist so the findings can be blunt. A VERIFIED finding is stated as fact. An ASSUMED one is stated as a claim with its assumption named. Neither is softened.
- Not commentary. The ledger, checkpoints, and logs are structured records. Prose goes in the closing note and nowhere else.
