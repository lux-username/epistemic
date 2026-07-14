---
name: find-pending-evidence
description: Step 3 of the crux-watchlist pipeline. For each crux, finds ongoing or upcoming evidence-gathering that would resolve it — registered trials, cohorts, pending reviews — capturing registry ID, what it measures, status, and expected report date. Borrows the registry-lookup concept; runs on web search alone, faster when a registry tool is present (runtime tool detection). A crux with no pending evidence found is a first-class finding, not an empty cell. 
---

# find-pending-evidence

Step 3. For each crux from `find-cruxes`, find the **pending evidence** that could resolve it, and produce the per-crux **`# Watch-list`** — the "check back when this reports" list that makes the artifact a *dated* object rather than a static summary. Schema: `.claude/skills/crux-watchlist/SKILL.md`.

This is the second half of the novel seam: crux → **pending evidence**. Living reviews and Trialstreamer monitor trials; what's new here is monitoring them *against a specific crux with a pre-committed update rule attached* (step 4). So a watch-list item is only worth anything if it actually bears on its crux.

## Input / output

- **In:** the `cruxes[]` from `find-cruxes` (each with a stable `crux_ref` like `C1`), and the crux `statement`/`why_it_moves` that say what "resolving it" means.
- **Out:** per crux, zero or more **watch-list items**, each: `crux_ref`, `source` (registry/ID + link), `measures`, `status`, `expected_report_date`; plus `gaps[]` additions for cruxes nobody is studying.

## What counts as pending evidence

Ongoing or upcoming evidence-gathering whose result isn't in yet: **registered trials** (recruiting/active), **prospective cohorts** with a scheduled readout, **pending systematic reviews** (PROSPERO-registered, not yet published), **pre-registered studies** with a stated completion date. Not pending: a preprint already posted, a published paper (that's step 1 evidence), or a vague "more research is needed."

**Relevance gate (hard).** A study only makes the watch-list if what it **measures would actually move its crux**. A trial measuring serum 25(OH)D does not resolve a crux about fracture *events*; a cohort in deficient adults does not resolve a crux about replete adults. State in `measures` *how* the readout maps to the crux's resolution — that mapping is what step 4 writes update rules against. Tangentially-related trials listed to look thorough are the same failure as a padded crux list: they manufacture a false sense that the crux is being watched.

## Runtime tool detection (role, not tool)

This step needs a **role filled — "a lookup of registered ongoing/upcoming studies and their expected report dates"** — not any specific tool. At runtime:

1. **Detect** what's actually available: a connected trial-registry MCP/API, a literature API, or nothing but web search.
2. **Use the most capable** relevant one for the role.
3. **Degrade gracefully** to Claude's built-in **web search** — the guaranteed floor — when nothing better is present.

Absence of a tool changes **thoroughness, never whether the step runs**. On the web-search floor: `site:clinicaltrials.gov` and `site:who.int/ictrp` searches plus fetching the trial record yield status and estimated completion date; PROSPERO pages yield in-progress reviews. This is best-effort, not systematic coverage — and that limitation is disclosed, not hidden (below).

**Retrieval is live and uncached** — query fresh each run so the watch-list reflects current registry reality (status and dates change). Consequence: runs aren't reproducible, and coverage depends on what's reachable at the time. Both are accepted tradeoffs, and both are disclosed in the artifact.

## Field discipline

- `source` — the real registry ID + link (e.g. `NCT########`, `CRD#########`, ISRCTN). **Never fabricate an ID.** If you can't verify a study exists with a real identifier, it does not go on the list — an invented trial is the worst possible failure here, a fictional promise of future resolution.
- `measures` — the outcome/endpoint *and* how it maps to the crux (the hook step 4 needs).
- `status` — one of: `recruiting | active | completed-unpublished | reported | terminated | withdrawn | unknown`. (This skill owns this enum.) Use `unknown` when the registry doesn't say, with a note.
- `expected_report_date` — from the registry's estimated primary-completion / study-completion date. May be `unknown` (e.g. registered but no date posted) — write `unknown — <reason>`, never guess a date.

## When to route to gaps

- **No pending evidence found for a crux** → a `gaps[]` entry: *"live crux, no registered ongoing study located (best-effort search)."* This is a **first-class finding** — a crux that moves the answer and that *nobody is studying* tells the reader exactly where the epistemic frontier is stuck. It is never a silent empty cell.
- **Best-effort coverage caveat** — because floor-level coverage is not systematic, mark the watch-list's coverage as best-effort; "found none" means "none located," not "none exist." Disclose, per the calibrated-humility commitment.
- **Uncertain relevance** — if you can't tell whether a study's readout would actually move the crux, it goes to `gaps[]` as a candidate to verify, not onto the watch-list as if confirmed.

## Output contract (illustrative)

```markdown
  Watch-list:
  - crux_ref: C1
    source: NCT01234567 — https://clinicaltrials.gov/study/NCT01234567
    measures: fractures / MACE (hard events) at 4y in baseline-deficient adults —
      directly tests whether raising 25(OH)D cuts *events* in the deficient subgroup, not just the biomarker
    status: recruiting
    expected_report_date: 2027-Q2
  - crux_ref: C1
    source: CRD42025XXXXXX (PROSPERO) — <link>
    measures: pooled fracture events across deficiency-stratified trials
    status: unknown — protocol registered, no timeline posted
    expected_report_date: unknown
```
For a crux with nothing found → `gaps[]`: *"C3 (dose-response ≥4000 IU/day) is live but no
registered trial or review located that stratifies dose finely enough."*

Values above are **illustrative**, not a worked case.

## Sources & methods

- Registry-lookup **concept** borrowed from public trial registries — **ClinicalTrials.gov** (*Courtesy of the U.S. National Library of Medicine*), **WHO ICTRP** (cite platform + processing date), **PROSPERO** (in-progress reviews). Modeled on **Trialstreamer** (Marshall et al.) as the closest existing ongoing-trial monitor — the *concept* is borrowed, not any API or code.
- We depend on none of these: the step runs on web search alone. When a user opts a registry API in, surface its courtesy attribution; its terms bind that opt-in run, not this skill.

*Named registries and products are trademarks/works of their owners; mention is not endorsement.*
