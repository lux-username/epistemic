---
name: lit-review
description: Step 1 of the crux-watchlist pipeline. Surveys the current evidence on the operationalized question, extracts claims with provenance, checks that each source actually backs its claim, grades per-study risk of bias (via appraise-study) and body-of-evidence certainty (GRADE-style), then states the current best-estimate answer, its confidence (which may be insufficient-evidence), and where the disagreement lives. Runs on web search alone; literature APIs are optional accelerators.
---

# lit-review

Step 1. Takes the operationalized question from `operationalize` (step 0) and produces two things: the artifact's **`# Current answer`** section (answer + confidence + one-paragraph basis), and a **graded evidence base** with the disagreement located — the seed the rest of the pipeline consumes. Schema: `.claude/skills/crux-watchlist/SKILL.md`.

This skill is the *orchestrating layer* of appraisal; the per-study risk-of-bias engine is the separate `appraise-study` component (`.claude/skills/appraise-study/`). Discovery and extraction run on **Claude's built-in web search alone**; the literature APIs named under *Sources & methods* are optional accelerators that raise recall without changing the method — if one is absent, the skill still completes.

**Keep a human in the steering loop.** The 2025 evidence is consistent that hybrid AI+human review beats fully-automated review, and that automated credibility scoring is *not reliably better than chance* (SCORE/repliCATS). So this skill surfaces its graded evidence and reasoning for inspection; it does not launder a confident answer out of weak inputs. Calibrated humility over confident error.

## Where this sits in the pipeline (read before running)

`appraise-study` grades a study *relative to the specific claim it bears on*, and the sharpest claims — the cruxes — aren't identified until step 2 (`find-cruxes`). So:

- **Here (step 1):** invoke `appraise-study` against the **primary operationalized question, treated as the root claim**. This yields the body-level certainty behind `current_answer`.
- **Step 2 (`find-cruxes`)** re-invokes the same component per crux to fill each crux's `current_evidence` grade.

Do not try to grade per-crux here — the cruxes don't exist yet. Locate *where* the disagreement lives; step 2 turns those locations into cruxes.

## Procedure

### 1. Discover the evidence
Search for the current evidence on the operationalized question, prioritizing by the standard hierarchy: systematic reviews / meta-analyses → randomized trials → cohorts → case-control / cross-sectional → mechanistic or expert opinion. Note recency — this question class is *fast-moving*, so favor the current evidence and flag anything likely superseded.

### 2. Extract claims with provenance
Pull the key claims that bear on the question. Every claim carries: the **source** (citation + link), its **study type**, its reported **effect/direction**, and its **population/outcome/exposure** (to check indirectness later). A claim with no locatable source is not a claim — it goes to `gaps[]`, never into the evidence base.

### 3. Provenance QA — does the source actually back the claim?
For each extracted claim, classify how well the cited source supports it, borrowing SemanticCite's four classes: **Supported · Partially supported · Unsupported · Uncertain**. `Unsupported` claims are dropped (and the misattribution noted); `Uncertain` are flagged. This guards against confidently citing a source that doesn't say what the claim needs — a failure mode more damaging than a missing citation.

### 4. Grade each source
- **Primary studies** → run `appraise-study` (RCTs through the full RoB mechanism; cohorts/case-control through its non-RCT gate), relative to the root claim. Carry its `low | some-concerns | high` grade and any gap flags.
- **Systematic reviews** → appraise with **AMSTAR-2 / ROBIS**, flagged **lower-confidence** (LLM–expert agreement is only ~58–70%, so a review's grade is itself uncertain). AMSTAR-2 is CC BY — its items may be quoted with citation, unlike RoB 2.

### 5. Roll up to body-of-evidence certainty (GRADE-style)
Combine the per-study grades into a certainty for the *body* of evidence on the question, applying GRADE's logic in our own words:

- **Start:** a body of RCTs starts at **High**; a body of observational studies starts at **Low**.
- **Downgrade** (one or two levels each, as serious/very-serious) for: **risk of bias** (from the `appraise-study` grades), **inconsistency** (studies genuinely disagree), **indirectness** (population/outcome/exposure drift from the operationalized question), **imprecision** (few events, wide intervals), **publication/reporting bias**.
- **Upgrade** (observational only): large effect, dose-response, or confounding that would only *shrink* the observed effect.
- **Floor:** Very-low. Result ∈ `High | Moderate | Low | Very-low`.

This is a GRADE-*based* judgment, not a certified GRADE rating — cite the GRADE Working Group, don't badge the output as "GRADE."

### 6. Weight claims and synthesize the current answer
Weight higher-certainty evidence more heavily; keep low-quality claims visible but down-weighted (Rootclaim's *idea* of weighting by direction and strength — qualitative, never a numeric likelihood-ratio computation we don't do). Then write `# Current answer`:

- **answer** — the best estimate, or an explicit "no answer supportable yet."
- **confidence** — map body certainty → the schema enum: High→`high`, Moderate→`moderate`, Low→`low`, Very-low→`low`. Use **`insufficient-evidence`** when even a best-estimate *direction* can't be responsibly given (no usable studies, or flat contradiction among only high-risk sources). `insufficient-evidence` is a valid, non-failing answer — not a fallback for laziness.
- **basis** — one paragraph: what the weighted evidence shows and why the confidence is what it is.

### 7. Locate the disagreement (hand-off to step 2)
State **where the disagreement actually lives** — the sub-questions on which high-quality evidence conflicts, is missing, or is indirect. These are the **candidate cruxes** `find-cruxes` (#9) will rank. This is the single most important output for the rest of the pipeline: a good current_answer with the disagreement mislocated produces a useless watch-list.

## When to refuse / route to gaps

- **No usable evidence located** → `current_answer` confidence `insufficient-evidence`; record what was searched and found empty in `gaps[]`.
- **Evidence too thin to grade** (a claim rests on sources `appraise-study` returns as `insufficient-evidence`) → don't invent a grade; log the ungradeable question in `gaps[]`.
- **Never** upgrade confidence to resolve discomfort with an ambiguous body of evidence. The acceptable failure is a well-scoped "insufficient evidence"; the failure we optimize against is confident misrepresentation.

## Output contract (illustrative)

```markdown
# Current answer

**Answer:** Roughly neutral for CVD in healthy adults at ~1 egg/day.
**Confidence:** low
**Basis:** Large prospective cohorts (body certainty: Low — observational start,
downgraded for residual-confounding risk of bias, no serious inconsistency) cluster
near null; the one feeding RCT addresses LDL, not events (indirect). No high-certainty
body supports a non-null effect in this population.
```
Plus, passed forward (not user-facing): the graded evidence base and the located
disagreement points — e.g. *"conflict lives in whether cholesterol-response
subgroups exist, and in diabetics where cohorts diverge"* → candidate cruxes for #9.

Values above are **illustrative**, not a worked case; the real eggs example is #17.

## Sources & methods

- Body-of-evidence certainty adapts **GRADE** (GRADE Working Group; Evidence-to-Decision framework) — applied in our own words; the output is GRADE-*based*, not a certified GRADE rating.
- Per-study risk of bias via `appraise-study`, which adapts **RoB 2** — Sterne JAC et al., *BMJ* 2019;366:l4898.
- Systematic-review appraisal: **AMSTAR-2** — Shea BJ et al., *BMJ* 2017;358:j4008 (CC BY, attribution required); **ROBIS** — Whiting P et al., *J Clin Epidemiol* 2016;69:225–234.
- Provenance QA (claim-support classes) adapts **SemanticCite** — arXiv:2511.16198.
- Humility priors: **SCORE / repliCATS** (automated credibility scoring ≈ chance); hybrid AI+human review outperforms full automation (Syst Rev 2024).
- Optional discovery/extraction accelerators (never required): Semantic Scholar API, Elicit, Consensus, Scite, Undermind. When a user opts into one, surface its courtesy attribution; none of their terms bind this skill, which runs on web search alone.

*Named frameworks and products are trademarks of their owners; mention is not endorsement; no Cochrane or GRADE Working Group endorsement is implied.*
