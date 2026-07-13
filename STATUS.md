> Generated 2026-07-13 by /end-session at commit 5cae1d7.

# STATUS

## Where things stand

**Session 6 acted on the first test's one real finding: the crux skills were all-prune, no-recall — now fixed, not yet validated.** Session 5 scored the omega-3 Run B (skills) vs A′ (base Claude) comparison and concluded "rigor, not gestalt." Tonight we pushed on the part that stung — *we tied base Claude on crux-finding, and crux-finding is the product's one-sentence pitch.* Reading the actual crux cards (not the count-based scorecard) showed the "tie" was two things: a **scoring artifact** (rubric counted cruxes; A′'s 6-vs-B's-3 included A′ inflating a near-settled question into a live crux) and **one real miss** (B silently dropped the Omega-3-Index / replacement-vs-enhancement camp).

Root cause, confirmed by auditing all seven pipeline skills for a *recall* counterweight to their *pruning* discipline: the failure is concentrated at the **lit-review → find-cruxes seam**, which was purely subtractive. `operationalize` and `write-update-rules` already carry a recall mechanism; the seam did not.

**Four edits made (design-of-record, not yet validated):**
- **lit-review** — added a **coverage sweep** (§1) for camps a top-down hierarchy search misses, and a §7 requirement to carry minority/dissenting camps forward with evidence status.
- **find-cruxes** — **candidate-recall sweep as a third source in step 1, *before* pruning** (so every candidate passes the same leverage→liveness→gaps gate — placement chosen deliberately to protect the honesty valve); a **"one gate only"** invariant (never append post-ranking); a **"Considered, not cruxes"** legibility line so a short list reads as a choice, not a miss.
- **comparison-rubric** (in `../epistemic_tests/`, un-versioned) — rewrote crux dimension to score **quality + completeness (union of both runs + field), not count**.

The design bet: recall at the front + honesty valve at the single exit lets us *out-surface* base Claude without endangering Run B's honesty-floor pass. **Unproven until the hard aspartame strip runs it and scores against the revised rubric.**

## Derived facts (from CLAUDE.md commands)

- Tracked `.md` files: **20** (`git ls-files '*.md' | wc -l`).
- `case-studies/`: does not exist (`ls case-studies/` → 0). No worked example yet (#17).
- Test status: no automated harness; validation is the clean-room runs (#19). **Run B passed (Session 5); crux-recall fix (Session 6) is unvalidated** — the aspartame strip is now both the generalization test *and* this fix's validation.
- HEAD `5cae1d7`; working tree at derivation: 2 modified skills (`find-cruxes`, `lit-review`) — committed with this session's docs.
- Open Issues: **18** (#13–#31) — `gh issue list --state open`. New this session: **#25–#31** (one wording-review issue per pipeline skill).

## Active Milestone

**Working prototype** — https://github.com/lux-username/epistemic/milestone/2 (due 2026-07-19). Correctness gate #19 met for omega-3; crux-recall fix applied but unvalidated. Remaining: wording reviews #25–#31, cross-cutting #13–#16, RoB auditability #23, quality gate #22 (de-egg, blocks #17), deliverables/validation #17–#21.

## Blockers / open questions

- **The recall fix is a hypothesis, not a result.** Next run must be the **hard aspartame strip**, scored against the revised (quality+completeness) rubric — it validates the fix where base Claude's recall advantage thins. Do not de-egg (#22) or write the showcase (#17) on the assumption the fix works until this run confirms it.
- **Reversed a registered decision.** Session 5 recorded "no skill fix warranted from A′." Session 6 supersedes that — A′ exposed the all-prune/no-recall gap and we acted. Logged in decisions.md; flagged here so the reversal isn't silent drift.
- **Test records still live outside git** (#24) — `../epistemic_tests/` now also holds tonight's revised `comparison-rubric.md`. Still un-backed-up; storage decision still pending.
- **Scite connector** remains disconnected (enforced Run B/A′'s web-search floor); reconnect before any tooled/scalability run.
