---
name: operationalize
description: Step 0 of the crux-watchlist pipeline. Turns an open, underspecified empirical question ("should adults take a vitamin D supplement?") into decision-relevant, testable framings — proposing them itself from the literature, picking the load-bearing one as primary, carrying the alternatives, and declaring the estimand (assignment vs adherence). Never demands information the user lacks. Runs on web search alone.
---

# operationalize

The question as posed is almost always underspecified — good/bad **for whom**, on **what outcome**, at **what dose**, over **what horizon**. This skill resolves that underspecification *itself*, from the literature, and emits the artifact's `# Question` section (see the schema in `.claude/skills/crux-watchlist/SKILL.md` → *Artifact schema*). Everything downstream — cruxes, appraisal, watch-list — is scoped to what this step declares.

**Non-negotiable:** never demand information the user doesn't have. The user brought a vague question *because* they don't know how to sharpen it — sharpening it is this skill's job, not theirs. Asking the user is a last resort, offered as an *option in the output*, never a blocking gate. (Acceptance test: the skill always returns a completed `# Question` section without a round-trip.)

## Input / output

- **In:** an open question in the class (unresolved *and* fast-moving in new study evidence).
- **Out:** the `# Question` section — posed form, one or more operationalizations with the **primary** marked and its **assumption** stated, each carrying an explicit **estimand**.

## Method

### 1. Enumerate framings along four axes

Search the literature for how this question is actually studied, then enumerate the decision-relevant framings on each axis. Do not invent axes the evidence doesn't populate; do not collapse axes the evidence separates.

| Axis | The question it forces | Vitamin D example (illustrative) |
|---|---|---|
| **Population — for whom** | Whose risk/benefit? Healthy adults, a diagnosed subgroup, an age/sex stratum? | replete healthy adults · baseline-deficient adults · older / institutionalized adults |
| **Outcome — good/bad how** | Which endpoint carries the decision? A hard event, a biomarker, all-cause mortality? | fracture / CVD events · serum 25(OH)D · all-cause mortality |
| **Dose / exposure — how much** | What contrast is even on the table? Define the exposure and its comparator. | 2000 IU/day vs placebo · high-dose bolus (≥100,000 IU/mo) vs daily |
| **Horizon — over what time** | Over what follow-up does the effect accrue or fade? | ~5 y · lifetime |

A framing is one coherent pick across all four axes. Enumerate the handful that are decision-relevant — not the full combinatorial grid.

### 2. Declare the estimand (assignment vs adherence)

For every framing, state **which effect the question is about** — this is inherited from RoB 2's assignment-vs-adherence distinction and it changes what counts as bias in step 1, so it must be fixed here, not left implicit:

- **Assignment / intention-to-treat (ITT)** — the effect of *being advised/assigned* the exposure, adherence imperfections included. The real-world, policy-relevant effect. Under this estimand, dropout and non-adherence are part of the answer, not bias.
- **Adherence / per-protocol (PP)** — the effect of *actually taking* the exposure as specified. The biological-efficacy effect. Under this estimand, differential adherence and loss to follow-up *are* bias and must be handled.

For observational questions ("do people with low vitamin D get more disease?"), name the **target trial** the cohort is emulating and which of its estimands is meant. Default to the estimand a typical asker's decision needs (usually ITT/real-world) and say so; carry the other only if a crux plausibly turns on it.

### 3. Pick the load-bearing primary; carry the rest

The **primary** is the framing whose answer most drives the decision for the *default asker* — the general person who'd type the question, absent further context. Choose it by asking which framing, if resolved, most changes what that person should do. Mark it `[primary]`. Keep the other decision-relevant framings as **carried alternatives** (they are not discarded — later steps may find a crux that lives in one of them).

### 4. State the assumption, out loud

Every primary pick embeds an assumption about who's asking and what they care about. State it in one line (e.g. *"CVD is the most decision-relevant outcome for the general asker"*). This is what a second person disagrees with and edits; it must be visible, not buried.

### 5. Offer, never gate

If a plausible asker would want a *different* primary (a deficient subgroup, a specific dose, a different outcome), surface that as an **offer in the output**, not a question back:

> *If you care specifically about vitamin-D-deficient adults, re-run noting that — the primary framing below assumes a replete general adult.*

Then proceed with the default. The skill must complete regardless of whether the user ever responds.

## When to refuse / route to gaps

Refusal here is scoped, never silent (see the "we don't know" convention in the schema):

- **Not an empirical question** (a value judgment, a definitional dispute) → don't fabricate framings; return `question: insufficient-evidence — not an empirical question in the class` and stop the pipeline.
- **Empirical but the literature is too sparse to enumerate framings** → state the posed question, mark the operationalization `insufficient-evidence`, and record the missing axes in the artifact's `gaps[]`. A well-scoped "can't yet frame this" is a valid output, not a failure.
- **Never** resolve underspecification by demanding the user supply it. That is the one failure mode this skill exists to prevent.

## Output contract (illustrative)

```markdown
# Question

**Posed:** Should adults take a daily vitamin D supplement?

**Operationalizations:**
- `[primary]` In replete (non-deficient) healthy adults, does daily vitamin D
  supplementation (2000 IU/day vs placebo) affect fracture and cardiovascular
  **events** over ~5 years?
  *Estimand:* assignment / intention-to-treat (emulates a target trial assigning
  2000 IU/day — a VITAL-analogue). *Assumption:* hard events (fractures, CVD) are the
  most decision-relevant outcome for the general asker.
- In baseline-deficient adults, same exposure and outcome. *(carried alternative)*
- Effect on all-cause mortality, healthy adults. *(carried alternative)*

> If you care specifically about a deficient subgroup or high-dose bolus dosing, re-run
> noting that — the primary assumes a replete general adult at 2000 IU/day.
```

Values above are **illustrative**, not a worked case.
