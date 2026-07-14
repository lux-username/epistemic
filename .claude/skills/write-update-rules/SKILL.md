---
name: write-update-rules
description: Step 4 of the crux-watchlist pipeline. For each crux × each plausible outcome of its watched evidence, pre-registers a conditional update — "if study X reports outcome A, shift the answer toward Y by a rough (qualitative) magnitude." Borrows Metaculus conditional-pair structure and Rootclaim's direction/strength-weighting idea, not numeric likelihood-ratio math. Sets the concrete recheck date. Runs on web search alone.
---

# write-update-rules

Step 4, the last stage before assembly. For each crux and each watch-list item under it, pre-register how the answer should move **when that evidence reports** — before the result is known. Produces the per-crux **`# Update rules`** and the top-level **`recheck_on`** date. Schema: `.claude/skills/crux-watchlist/SKILL.md`.

This is the third piece of the novel seam — crux → pending evidence → **pre-registered update rule** — and the piece nobody else wires up: living reviews re-synthesize when a study lands; Metaculus has conditional pairs but not over a graded evidence base; here the rule is committed *in advance*, against a specific readout, so whoever holds this artifact later can apply it mechanically instead of re-litigating.

## Input / output

- **In:** the `cruxes[]` (with `why_it_moves` — the sensitivity) and their `watch_list[]` items (with `measures` — how each readout maps to the crux). The `measures→crux` mapping that `find-pending-evidence` enforced is exactly what a rule is written against.
- **Out:** per crux, a set of update rules keyed to its watch-list items; plus the artifact's `recheck_on`.

## The discipline: pre-registration

An update rule is only worth anything if it's written **before** the outcome is known and **enumerates the plausible outcomes in advance** — that's what makes it a commitment rather than a post-hoc rationalization. Borrow **Metaculus's conditional-pair structure**: "*if* [watched study] resolves [outcome A], *then* the answer becomes [state]; if [outcome B], then [other state]." For each watch-list item, enumerate its **plausible, mutually-exhaustive outcomes** — typically:

- a result in the hypothesized direction (effect confirmed),
- a null / no-effect result,
- and the honest third rail: **inconclusive / underpowered / terminated / never-reported** — which must map to *no update* (and often a `gaps[]` note), never to a default lean.

Writing the "it didn't really answer it" branch is not optional — a study that reports ambiguously is the common case, and a rule that only covers "confirms" vs "refutes" quietly launders ambiguity into a fake signal.

## Direction and magnitude (qualitative only)

Each branch states a **direction** (toward which answer/state the belief moves) and a **rough magnitude**. Borrow **Rootclaim's idea** — weight a piece of evidence by the direction *and strength* it points — as the *reasoning*, **not** its numeric likelihood-ratio → posterior math (which we do not do and do not claim; it's cited with its public failure as a caution against garbage-in likelihoods).

Magnitude is a **qualitative band**, never a number:

- `negligible` — barely moves the answer (e.g. a small underpowered trial confirming what high-certainty evidence already shows).
- `small` — nudges confidence or narrows a range.
- `moderate` — meaningfully shifts the answer or its confidence.
- `large` — could flip the answer or overturn the current stance.

Calibrate magnitude to two things already in the artifact: the crux's **leverage** (`why_it_moves` — how much the answer depends on it) *and* the watched study's **weight** (its design and, once it reports, its appraised quality — a large well-run RCT earns a larger update than a small observational readout). A rule that promises a `large` update from a study too weak to deliver one is an overclaim; say so and cap the magnitude.

## Recheck date

Set the artifact's `recheck_on` from the watch-list's **earliest meaningful `expected_report_date`** — the soonest a watched study could actually change something — not an arbitrary interval. If several cluster, pick the first; if all dates are `unknown`, set `recheck_on` to a bounded default (e.g. 6 months) and note *why* it's a default rather than a data-driven date. The recheck date is a promise about when this artifact becomes worth revisiting; make it honest.

## When to refuse / route to gaps

- **No watch-list item for a crux** (the crux went to `gaps[]` in step 3 as "nobody studying it") → no update rule to write; leave a `gaps[]` note that this crux has no trigger, so a reader knows it won't self-resolve.
- **Can't enumerate plausible outcomes** for a study (its design or endpoints are too unclear to say what "confirming" would even look like) → don't fabricate branches; record the item as watched-but-unruled in `gaps[]`.
- **Never** write a rule that updates on *any* outcome (including inconclusive) toward a preferred conclusion. If every branch points the same way, the rule is broken — you've encoded a bias, not a conditional update.

## Output contract (illustrative)

```markdown
  Update rules:                          # under crux C1, keyed to its watch-list
  - on: NCT01234567 (fractures/MACE at 4y, baseline-deficient adults)
    - if ↓events, CI excludes null → toward "supplement if deficient", **moderate**
      (large RCT on hard events, directly on-crux)
    - if null (CI around no effect) → toward "neutral even if deficient", **small**
      (confirmatory of the replete-population null)
    - if underpowered / terminated / unreported → **no update**; note in gaps
  - on: CRD42025XXXXXX (pooled fracture events)
    - if pooled effect ≠ null → toward "benefit in deficient", **small** (meta-analytic
      pooling; between-trial heterogeneity caps the update)
    - if null → toward "neutral", **negligible**
    - if never published → no update; gaps note

recheck_on: 2027-Q2   # earliest meaningful expected_report_date on the watch-list
```

Values above are **illustrative**, not a worked case.

## Sources & methods

- Conditional-pair structure borrowed from **Metaculus** conditional pairs (resolution discipline) — structure, not content.
- Direction/strength weighting borrows the **idea** from **Rootclaim** (how-it-works) — qualitative only; **no** numeric likelihood-ratio → posterior computation is performed or claimed. Cited with its documented public failure as a caution about garbage-in likelihoods.
- Update-magnitude reasoning is informed by **GRADE Evidence-to-Decision** (how certainty of evidence bears on a decision) — applied in our own words, not badged as GRADE.

*Named frameworks and products are trademarks/works of their owners; mention is not endorsement.*
