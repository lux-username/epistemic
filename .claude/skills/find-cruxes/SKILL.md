---
name: find-cruxes
description: Step 2 of the crux-watchlist pipeline. Takes lit-review's located disagreement and finds the sub-questions that most move the answer — ranked by the qualitative logic of value of information (leverage × liveness), not numeric EVPPI. For each crux it states why it moves the answer and grades the current evidence (via appraise-study). Suspected-but-unsubstantiated cruxes go to gaps, never into the crux list dressed up as established. Runs on web search alone.
---

# find-cruxes

Step 2. Consumes `lit-review`'s output — the `current_answer` and the **located disagreement** — and produces the artifact's **`# Cruxes`** section: the ranked sub-questions whose resolution would most change the answer, each with its sensitivity and its graded current evidence. Schema: `.claude/skills/crux-watchlist/SKILL.md`.

This is the seam that makes the tool *this* tool: not "here's what the literature says," but "here's the specific unknown that, when it resolves, changes what you should believe." Everything downstream (pending evidence, update rules) hangs off the cruxes named here — so a mislocated crux poisons the whole back half. Get this honest, not impressive.

## Input / output

- **In:** `current_answer` + graded evidence base + located disagreement points from `lit-review` (step 1).
- **Out:** `# Cruxes` — a short ranked list, each with `statement`, `why_it_moves`, and `current_evidence` (grade from `appraise-study`); plus additions to `gaps[]`.

## The crux test (qualitative VOI)

A sub-question is a crux **to the degree it is both**:

1. **Leverage** — resolving it one way vs. the other would *materially change the overall answer*. (How big is the swing? Does the answer flip, shift a lot, or barely move?)
2. **Liveness** — it is *genuinely unresolved right now*; the current evidence doesn't already settle it.

Both are required. Borrowing the logic of **value of information**: the value of resolving an unknown is high only when the answer both *depends on it* (leverage) and *doesn't yet know it* (liveness). A high-leverage question the evidence has already settled has **no** VOI — it's background, not a crux. A wide-open question the answer doesn't hinge on is trivia. We borrow this reasoning as a **qualitative lens to rank cruxes — never a numeric EVPPI computation**; we have no quantified decision model and claim no EVPPI number.

The plain-language name for the same idea is **double crux**: a point where, if it resolved the other way, you would actually change your conclusion. State each crux that way — if you *wouldn't* change your answer on either resolution, it isn't one.

## Procedure

1. **Assemble candidates.** Start from lit-review's located disagreement, then scan the `current_answer`'s basis for the load-bearing assumptions it rests on. Each is a candidate sub-question.
2. **Score leverage.** For each candidate, ask: if it resolved toward X, what does the overall answer become? Toward Y? The *distance between those two answers* is its leverage. Small distance → drop it.
3. **Score liveness.** Consult the graded evidence base: is this genuinely open, or does existing high-certainty evidence already settle it? Settled → drop it (note it as established background, not a crux).
4. **Rank** by leverage × liveness (qualitatively — "this one flips the answer and is wide open" > "this one shifts it a little and is mostly settled"). Keep the handful that genuinely move the answer. **Do not pad to a quota** — a short honest list beats a long dressed-up one.
5. **Fill each surviving crux:**
   - `statement` — the sub-question, phrased as a double crux (resolvable, with the two directions implied).
   - `why_it_moves` — the sensitivity, *both directions*: "if X → answer becomes A; if Y → answer becomes B." This is the leverage made explicit.
   - `current_evidence` — invoke **`appraise-study`** (`.claude/skills/appraise-study/`) on the studies bearing on *this crux*, relative to *this crux*. This is where the per-crux, per-result grading happens (deferred here from step 1 by design). Carry the `low | some-concerns | high` grades and any gap flags.
6. **Route to gaps.** Send to `gaps[]`:
   - **Suspected but unsubstantiated cruxes** — you hypothesize the answer hinges on it, but can't show *either* the leverage or the liveness from the evidence. It does **not** go in the crux list dressed as established.
   - **Cruxes you can't grade** — the crux is real but no appraisable evidence bears on it yet (a live crux nobody has studied — itself a valuable finding, and exactly what step 3 will look for).

## When to refuse / humility

- **No identifiable crux.** If the answer isn't materially sensitive to any locatable sub-question — because the evidence is uniformly strong (settled) or uniformly absent (nothing to hinge on) — say so plainly rather than manufacturing cruxes. Uniform absence routes to `gaps[]` with `current_answer` confidence `insufficient-evidence`; uniform strength means a short "no live cruxes; the answer is settled" is the honest output.
- **Never invent a crux to look thorough.** A fabricated crux propagates into a fabricated watch-list item and a fabricated update rule — the worst failure mode, three steps of confident fiction from one padded list. When unsure whether something is a crux, it goes to `gaps[]`, not the list.

## Output contract (illustrative)

```markdown
# Cruxes

## C1 — Does dietary cholesterol from eggs raise CVD events independent of overall diet pattern?
- **why_it_moves:** if yes → answer shifts from "neutral" toward "limit for at-risk
  groups"; if no → current neutral stance holds and firms up. High leverage, live
  (cohorts confounded by diet pattern; no RCT on events).
- **current_evidence:** 2 large cohorts near-null (grade: `some-concerns` — residual
  confounding); 1 feeding RCT on LDL only, not events (grade: `low` for its endpoint,
  but indirect to this crux).

## C2 — ...
```
Suspected-but-unsubstantiated (→ `gaps[]`), e.g.: *"dose-response above 2 eggs/day may
be a crux, but no study stratifies finely enough to show the answer hinges on it —
not asserted as a crux."*

Values above are **illustrative**, not a worked case; the real eggs example is #17.

## Sources & methods

- Crux ranking borrows the **reasoning** of **Value of Information / EVPI / EVPPI** (Raiffa & Schlaifer, *Applied Statistical Decision Theory*, 1961; Claxton et al.) as a **qualitative lens only** — no numeric EVPPI is computed or claimed.
- The plain-language framing is **Double Crux** (CFAR / LessWrong) — described, not reproduced.

*Named frameworks are trademarks/works of their owners; mention is not endorsement.*
