---
name: appraise-study
description: Component of step 1 (lit-review). Appraises the risk of bias of one study result relative to the specific crux it bears on, adapting the RoB 2 mechanism — 5 bias domains, our own signaling questions, a deterministic aggregation. The model answers only near-factual signaling questions; a fixed algorithm assigns the grade. "No Information" is a humility valve that routes to gaps, never to a clean pass. Runs on web search alone.
---

# appraise-study

Grades how much a **single study result** should be trusted *for a specific crux*. Invoked by `lit-review` (step 1) once per (study × crux) pair; its output populates that crux's `current_evidence` grade and feeds any unjudgeable domains to `gaps[]` (schema: `.claude/skills/crux-watchlist/SKILL.md`).

**Why the design is shaped this way.** We reuse the RoB 2 *mechanism* — not its wording (its signaling-question text is CC BY-NC-ND; we describe the bias mechanism and author our own questions). The mechanism's whole value is a **division of labor**: the model answers only near-factual questions about what a paper reports, and a **fixed algorithm** — not the model — maps those answers to a grade. 2025 evaluations find LLMs reliable at the signaling questions and unreliable at the overall verdict, so the verdict must never be a model judgment. That is this skill's core acceptance test.

## Input / output

- **In:** one study; the **crux** it bears on; the **estimand** declared by `operationalize` (assignment/ITT vs adherence/per-protocol — it changes what counts as bias in Domain 2).
- **Out:** a per-domain judgment table, an **overall risk-of-bias grade** ∈ `low | some-concerns | high`, and zero or more **gap flags** for domains that could not be judged because the paper doesn't report them.

Appraisal is **per result, relative to the crux** — the same trial may be low-risk for one crux (its primary endpoint) and high-risk for another (an underpowered subgroup). Never grade "the study" in the abstract.

## Response set

Every signaling question is answered with exactly one of: **Yes · Probably Yes · Probably No · No · No Information**. The model answers from what the paper reports; it does not guess. Each question below declares `low_risk_when:` so the algorithm — not the model — knows which direction is reassuring. `No Information` (NI) is never treated as reassuring.

## Study-type gate (run first)

- **Randomized trial** → run all five domains below.
- **Non-randomized** (cohort, case-control, cross-sectional — most observational evidence) → the randomization and ITT machinery doesn't apply cleanly. Report `design: non-RCT`, run Domains 3–5 (they transfer), replace Domain 1 with a single confounding check (*"Were the main confounders for this crux identified and adjusted for?"*, `low_risk_when: Yes`), **cap the overall grade at `some-concerns`**, and hand the body-of-evidence certainty judgment to `lit-review`'s GRADE step. Rationale: honestly flagging "this needs a different instrument" beats forcing an RCT tool onto a cohort.

## The five domains

For each: the **bias mechanism** in our own words, **our** signaling questions, and a **deterministic per-domain rule**. `Yes/PY` = the answer; `No/PN` = its negation; `NI` = No Information.

### D1 — Arising from the randomization process
*Mechanism: if allocation isn't truly chance-based, or the next assignment is foreseeable, enrollers can steer who gets what, so the groups differ at baseline and confounding is mistaken for effect.*

| # | Signaling question | low_risk_when |
|---|---|---|
| 1.1 | Was the allocation sequence produced by a genuinely chance-based method? | Yes |
| 1.2 | Was the next assignment concealed from enrollers until the moment of allocation? | Yes |
| 1.3 | Are baseline differences between groups within what chance alone would produce? | Yes |

**Rule:** `High` if any of 1.1/1.2/1.3 is `No/PN`. `Low` if all three are `Yes/PY`. Otherwise (including any `NI`) → `Some concerns`.

### D2 — Deviations from intended interventions *(branches on estimand)*
*Mechanism: what happens after assignment can bias either the real-world effect (unblinded behavior/co-interventions differing by arm) or the biological effect (non-adherence). Which of these is bias depends on the estimand the question declared.*

| # | Signaling question | low_risk_when |
|---|---|---|
| 2.1 | Were participants and intervention-providers unaware of assignment (or is the outcome one that awareness couldn't plausibly affect)? | Yes |
| 2.2 · **ITT** | Were all randomized participants analyzed in their assigned group, regardless of what they received? | Yes |
| 2.3 · **ITT** | Did assignment-driven care differences (beyond the intervention itself) arise that could shift the outcome? | No |
| 2.2 · **PP** | Was adherence high and balanced across groups? | Yes |
| 2.3 · **PP** | If adherence was imperfect, was an appropriate method used to estimate the per-protocol effect? | Yes |

**Rule (ITT):** `High` if 2.2-ITT is `No/PN` (not analyzed as assigned) **or** 2.3-ITT is `Yes/PY` (outcome-shifting deviations). `Low` if 2.1, 2.2-ITT are `Yes/PY` and 2.3-ITT is `No/PN`. Otherwise / any `NI` → `Some concerns`.
**Rule (PP):** `High` if 2.2-PP is `No/PN` **and** 2.3-PP is `No/PN` (large unadjusted non-adherence). `Low` if 2.1, 2.2-PP, 2.3-PP are all `Yes/PY`. Otherwise / any `NI` → `Some concerns`.

### D3 — Missing outcome data
*Mechanism: if outcomes are missing for a non-trivial share of participants and whether data is missing depends on the outcome itself, the observed effect is distorted.*

| # | Signaling question | low_risk_when |
|---|---|---|
| 3.1 | Were outcome data available for nearly all randomized participants? | Yes |
| 3.2 | If a meaningful share was missing, is the result shown robust to plausible missingness (or missingness plausibly unrelated to the outcome)? | Yes |

**Rule:** `Low` if 3.1 is `Yes/PY`. If 3.1 is `No/PN`: `Low` if 3.2 is `Yes/PY`; `High` if 3.2 is `No/PN`; `Some concerns` if 3.2 is `NI`. Any `NI` on 3.1 → `Some concerns`.

### D4 — Measurement of the outcome
*Mechanism: an outcome ascertained differently between arms, or scored by assessors who know the assignment, is most dangerous when the outcome is subjective enough for that knowledge to color it.*

| # | Signaling question | low_risk_when |
|---|---|---|
| 4.1 | Was the outcome measured the same way and with an appropriate method in both groups? | Yes |
| 4.2 | Were outcome assessors unaware of assignment? | Yes |
| 4.3 | Is the outcome subjective enough that knowing the assignment could influence its measurement? | No |

**Rule:** `High` if 4.1 is `No/PN`, **or** (4.2 is `No/PN` **and** 4.3 is `Yes/PY`) — an unblinded assessor scoring an influenceable outcome. `Low` if 4.1 is `Yes/PY` **and** (4.2 is `Yes/PY` **or** 4.3 is `No/PN`) — either blinded, or the outcome is objective. Otherwise / any `NI` → `Some concerns`.

### D5 — Selection of the reported result
*Mechanism: when many outcomes, time points, or subgroups could be reported, choosing which to show after seeing the data manufactures apparent effects; a pre-specified analysis plan is the guard.*

| # | Signaling question | low_risk_when |
|---|---|---|
| 5.1 | Did the reported analysis follow a plan finalized before unblinded outcome data were available (registered protocol / SAP)? | Yes |
| 5.2 | Does the reported result look chosen from several measures/analyses because of how it came out? | No |

**Rule:** `High` if 5.2 is `Yes/PY`. `Low` if 5.1 is `Yes/PY` **and** 5.2 is `No/PN`. Otherwise (including no available protocol → 5.1 `NI`) → `Some concerns` — absence of a protocol can never earn `Low`.

## Overall aggregation (fixed — the model does not decide this)

Apply in order to the five (or, for non-RCTs, transferred) domain judgments:

1. Any domain `High` → **overall `high`**.
2. Else any domain `Some concerns` → **overall `some-concerns`**.
3. Else (all `Low`) → **overall `low`**.
4. Non-RCT designs are capped at `some-concerns` (never `low`).

**No Information → gaps (humility valve).** Whenever a domain lands on `Some concerns` *because a critical question was answered `NI`*, emit a gap flag `{domain, "unjudgeable — not reported"}` and record it. A study with any `NI`-driven domain **can never be graded overall `low`** — an under-reported trial resolves to `some-concerns`, never a fabricated clean pass. `lit-review` carries these flags to the artifact's `gaps[]` and weights the crux's evidence accordingly.

## When to refuse

- **Wrong instrument / not a study:** if the "source" isn't a primary study or review (a news article, a blog, an unlinked assertion), don't run the domains — return `grade: insufficient-evidence — not an appraisable study` so `lit-review` doesn't weight it as evidence.
- **Can't access enough of the paper to answer the gating questions:** return `grade: insufficient-evidence` with the reason; do not infer a grade from the abstract alone.

## Output contract (illustrative)

```yaml
study: "Author 2024, RCT, primary CVD endpoint"
crux_ref: C1
estimand: ITT
design: RCT
domains:
  D1_randomization: low
  D2_deviations: some-concerns   # 2.2-ITT = No Information
  D3_missing_data: low
  D4_measurement: low
  D5_selective_reporting: some-concerns   # no protocol found → 5.1 NI
overall: some-concerns
gap_flags:
  - { domain: D2_deviations, note: "ITT-vs-analyzed handling not reported" }
  - { domain: D5_selective_reporting, note: "no pre-registered protocol located" }
```

Values above are **illustrative**, not a worked case.

## Sources & methods

We adapt the **mechanism** of the Cochrane **RoB 2** tool — its five-domain structure, five-point response set including *No Information*, and factual-questions-then-fixed-algorithm division of labor — under 17 U.S.C. § 102(b) (methods aren't copyrightable, only their specific expression). **All signaling-question wording and all aggregation rules here are our own**, authored from each domain's bias mechanism; none reproduce or paraphrase Cochrane's text (RoB 2 signaling questions are CC BY-NC-ND — describe only).

- **RoB 2** — Sterne JAC, Savović J, Page MJ, et al. *BMJ* 2019;366:l4898.
- Body-of-evidence certainty (High/Moderate/Low/Very-low) is **GRADE** (GRADE Working Group), applied by `lit-review`. This tool grades individual results only; it is **not** GRADE and does not carry Cochrane's endorsement.

*Named frameworks and products are trademarks of their owners; mention is not endorsement.*
