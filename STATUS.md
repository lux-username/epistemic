> Generated 2026-07-13 by /end-session at commit 2ed81b9.

# STATUS

## Where things stand

**First behavioral tests of the prototype are done — and the correctness gate is green for one question.** This session was the *command center*: it planned, then analyzed (the skills were run in separate clean-room sessions, not here). Two runs from the pre-registered #19 design, both on a **non-egg** question (**omega-3 for CVD**), same web-search-+-fetch floor, no connectors:

- **Run B — self-containment / honesty floor: PASS, decisively.** 0 of 5 pre-registered failure modes materialized. The trace confirms all stage skills were actually opened and run (not simulated); all 4 cited registry IDs verify and are accurately described; per-domain RoB appraisal genuinely executed (No-Information valve fired); estimand threaded; cruxes are the real expert disagreement. The clincher: for the load-bearing crux (is REDUCE-IT's benefit a mineral-oil-placebo artifact?) **no confirmatory trial exists, and the run refused to fabricate one** — routing the absence to `gaps[]` as the headline finding. It even found a superficially-perfect trial (MITIGATE) and correctly excluded it (respiratory outcome, not cardiac).

- **Run A′ — framing-matched control (un-scaffolded base Claude, same task):** ties B on **finding cruxes and the central honest refusal** (base Claude is already good at these on a familiar question), but B wins clearly on **rigor/verification** — A′ ran *zero* fetches (verified nothing), carried one real-but-**observational/surrogate** ID mis-assigned as an RCT resolver, filed an already-reported dialysis trial (PISCES) into the *pending* slot, used numeric false-precision confidences, and did no systematic grading. **Honest conclusion: the method's value is rigor, not gestalt** — which is why the next test must be a *hard* question where base Claude's recall advantage vanishes.

## Derived facts (from CLAUDE.md commands)

- Tracked `.md` files: **19** (`git ls-files '*.md' | wc -l`).
- `case-studies/`: does not exist (`ls case-studies/` → 0). No worked example yet (#17).
- Test status: no automated harness; validation is the clean-room runs (#19). **Run B passed; A′ control scored.** Records + full scored analyses live in **`../epistemic_tests/`** (a sibling folder, **not** version-controlled — see Blockers).
- HEAD `2ed81b9`; working tree clean at derivation (this session's work is the doc updates in this commit; test artifacts are external).
- Open Issues: **11** (#13–#23) — `gh issue list --state open`. New this session: **#23**.

## Active Milestone

**Working prototype** — https://github.com/lux-username/epistemic/milestone/2 (due 2026-07-19). Correctness gate #19 **met for omega-3** (two scorecards posted as #19 comments). Remaining: cross-cutting #13–#16, #23 (RoB auditability, low), quality gate #22 (de-egg, blocks #17), deliverables/validation #17–#21.

## Blockers / open questions

- **Test records live outside git.** `../epistemic_tests/` (briefs, rubrics, artifacts, traces, scored analyses for runs B and A′) is a local sibling folder, not a repo — currently un-backed-up. Decide whether to commit a distilled copy into this repo, add it as its own repo, or accept it as scratch. The *conclusions* are preserved in the #19 comments; the raw artifacts are not.
- **#19 is not fully closed** — omega-3 passed, but it's a *soft* strip (trial-shaped, in training). The honest generalization claim needs the **hard aspartame strip** (regulatory + observational, non-RCT-shaped) — staged conceptually, not yet run. Sequence unchanged: hard validation → fixes → #22 de-egg → #17 eggs showcase → #21 writeup.
- Scite connector was disconnected at the account level to enforce Run B/A′'s web-search floor; **reconnect before any Run C** (tooled/scalability).
