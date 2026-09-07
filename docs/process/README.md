# docs/process

The build record and reusable process docs for a Tentacles release pass. Each versioned pass (starting with v1.4) leaves its task prompt, audit report, and decision log here so the next pass — and any fork rebasing onto it — starts from what was actually found and decided, not from memory. Files marked reusable in their header are meant to be copied and adapted; the rest are one-time records. `decisions.md` is **append-only**: every gated approval block is appended verbatim as it happens — nothing in it is rewritten or summarized after the fact, so it can be read as the sequence of calls actually made.

| File | What it is |
|---|---|
| [`v1.4-audit-prompt.md`](v1.4-audit-prompt.md) | The Claude Code task prompt for the v1.4 pass — ground rules, Phase A audit spec, Phase B build spec (reusable) |
| [`reflection-protocol.md`](reflection-protocol.md) | R0–R5 self-check protocol for audit runs — untested as of v1.4, first real run planned for cl-1.0 → cl-1.1 (reusable once validated) |
| [`phase-a-audit.md`](phase-a-audit.md) | Phase A audit report against v1.3 with the approved change list (one-time record) |
| [`decisions.md`](decisions.md) | Phase A approval and per-section diff-review decisions for the v1.4 build (one-time record) |
