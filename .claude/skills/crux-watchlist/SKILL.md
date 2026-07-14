---
name: crux-watchlist
description: For an open, fast-moving empirical question, produce a dated crux watch-list artifact — the cruxes that most move the answer, the pending evidence that may resolve them, and pre-registered rules for how to update when it reports. Runs on Claude's built-in web search alone; suggested tools are optional accelerators, never dependencies.
---

# crux-watchlist

Orchestrator. Takes an open question in the class (unresolved *and* fast-moving in new study evidence), runs the 5-step pipeline, and emits one **crux watch-list artifact** in the schema below.

The artifact is the compounding unit — a self-contained, hand-offable file someone else can fold into a larger epistemic project, disagree with cell-by-cell, and act on when a watched study reports.

## Orchestration (steps 0–5)

Run the pipeline in order, threading each stage's output into the next, and assemble the result into the **Artifact schema** below. Each stage skill lives in its own folder under `.claude/skills/` and carries its own method, refusal conditions, and worked micro-example; this section is the conductor, not a re-statement of them.

**Runs autonomously to a complete artifact.** Like `operationalize`'s never-gate rule, the orchestrator never *blocks* on the user: it surfaces its reasoning and flags points worth inspection (calibrated humility favors a human in the loop), but always completes on its own. A run that can't answer still emits a well-formed, mostly-`gaps[]` artifact — that is a success, not a crash.

### Step −1 · Detect tools once, up front

Before step 0, check **which retrieval tools are actually available** this run — connected trial-registry / literature MCP servers or APIs, and Claude's built-in web search (the guaranteed floor). Carry that picture into the stages that retrieve (`lit-review`'s discovery, `find-pending-evidence`'s registry lookup): each fills a **role** ("literature search," "trial-registry lookup") with the most capable tool on hand, degrading to web search when nothing better is present. **Tool absence changes thoroughness, never whether a step runs**, and thin coverage is disclosed in `gaps[]`, never hidden. No stage may hard-depend on any specific tool or API.

### Steps 0–4 · Run the stages, threading state

| Step | Skill | Consumes | Produces |
|---|---|---|---|
| 0 | `operationalize` | the raw question | `# Question` — operationalizations, primary marked, **estimand** per framing |
| 1 | `lit-review` | the **primary** operationalization | `# Current answer` + graded evidence base + *located disagreement* |
| 2 | `find-cruxes` | current_answer + located disagreement | `# Cruxes` (`C1…`, each with graded `current_evidence`) |
| 3 | `find-pending-evidence` | the cruxes | per-crux `# Watch-list` |
| 4 | `write-update-rules` | cruxes + watch-list | per-crux `# Update rules` + `recheck_on` |

Three things only the conductor can thread — get these right or the stages silently mis-fire:

1. **Estimand.** `operationalize` declares the estimand (assignment/ITT vs adherence/PP) on the primary framing. **Pass it into every `appraise-study` invocation** — both `lit-review`'s root-claim appraisal (step 1) and `find-cruxes`' per-crux appraisal (step 2) — because `appraise-study` Domain 2 branches on it. Cruxes inherit the primary framing's estimand.
2. **Gaps aggregation.** *Every* stage can emit `gaps[]` entries (unoperationalizable framings, ungradeable evidence, suspected-but-unsubstantiated cruxes, cruxes with no pending evidence, un-ruleable watch items). Collect them all into the single `# Gaps` section — deduplicated, each traceable to the step that raised it.
3. **Degrade, don't crash.** If a stage refuses (`operationalize` on a non-empirical question; `lit-review` with no usable evidence → `insufficient-evidence`), do **not** abort. Stop threading where the evidence runs out, fill the reachable sections, mark the rest `insufficient-evidence`/`unknown`, and route the reason to `gaps[]`. The artifact is always well-formed.

### Step 5 · Assemble

Collate into one artifact per the **Artifact schema** below:

- **Frontmatter:** `schema_version`, `question_id` (a slug from the primary operationalization, matching the filename), `as_of` (**stamp with the current date at runtime**), `recheck_on` (from step 4).
- **Body, crux-centric:** `# Question`, `# Current answer`, then `# Cruxes` where **each crux subsection nests its own `# Watch-list` and `# Update rules`** (so everything about one crux is edit-in-one-place), and finally `# Gaps`.

Verify before emitting: every `crux_ref` in the watch-list and update-rules resolves to a listed crux; every schema field is present or explicitly `unknown`/`insufficient-evidence` (never silently absent); `recheck_on` is set.

### Acceptance (this skill's contract)

- **Runs cold** — a fresh Claude in a folder of only these skills, given a question, reaches a complete artifact with no prior conversation or external notes.
- **Tool-agnostic** — detects tools at runtime, degrades to web search, depends on no external API/tool; absence lowers thoroughness only.
- **Honest by construction** — refusals degrade to a well-formed gaps-rich artifact rather than fabricated content.

## Artifact schema

The output is one Markdown file per question: a small YAML frontmatter (top-level scalars a tool or knowledge base indexes on) followed by body sections carrying everything substantive. **Every fact has one home** — a crux, its evidence, its watch-items, and its update rules appear once, together, in that crux's subsection. Nothing in the body is duplicated into frontmatter.

### The "we don't know" convention (load-bearing)

The schema makes refusal representable so the process is never forced to invent. **Any** field may take one of two reserved tokens in place of a fabricated value:

- **`unknown`** — a specific fact was not established (e.g. a trial's report date is not posted).
- **`insufficient-evidence`** — the *evidence base itself* is too thin to answer. This is the named lowest rung of `confidence`, and the reason a suspected crux is routed to `gaps` rather than asserted.

Either token may carry a trailing ` — <note>` giving the reason (e.g. `unknown — registry lists no primary completion date`). Populating these honestly is a success, not a shortfall.

### Frontmatter

```yaml
schema_version: 1
question_id: vitamin-d-events     # slug; matches the filename
as_of: 2026-07-12                 # date generated; everything below is relative to it
recheck_on: 2026-11-01            # date to re-run/revisit — or: insufficient-evidence
```

### Body

```markdown
# Question

**Posed:** Should adults take a daily vitamin D supplement?

**Operationalizations:**
- `[primary]` In replete (non-deficient) healthy adults, does daily vitamin D
  supplementation (2000 IU/day vs placebo) affect fracture and cardiovascular
  events over ~5 years?
  *Assumption:* hard events (fractures, CVD) are the most decision-relevant outcome
  for the general asker.
- In baseline-deficient adults, same exposure and outcome. *(carried alternative)*
- Effect on all-cause mortality. *(carried alternative)*

# Current answer

**Answer:** Roughly neutral for CVD, cancer, and fractures in replete adults at 2000 IU/day.
**Confidence:** moderate       <!-- high | moderate | low | insufficient-evidence -->
**Basis:** Large RCTs (VITAL and peers) cluster near null for hard events; supplementation
raises serum 25(OH)D but that rise hasn't translated to event reduction in replete adults,
and the deficiency-stratified subgroup evidence is thin. (one paragraph)

# Cruxes

## C1 — Does raising serum 25(OH)D via supplementation cut hard events (fractures/CVD) in baseline-deficient adults, independent of the replete-population null?

- **why_it_moves:** If yes, the answer shifts from "neutral, skip it" toward "supplement
  the deficient"; if no, the current neutral stance holds.
- **current_evidence:** large RCTs near-null overall (grade: `some-concerns`, enrolled
  few deficient participants); deficiency-stratified subgroup analyses suggestive but
  underpowered (grade: `low` for that endpoint). <!-- grade = risk-of-bias from appraise-study -->

  **Watch-list:**
  - `source:` NCT01234567 — https://clinicaltrials.gov/study/NCT01234567
    `measures:` fractures/MACE at 4y in baseline-deficient adults
    `status:` recruiting            <!-- status enum owned by find-pending-evidence -->
    `expected_report_date:` 2027-Q2
  - `source:` PROSPERO-CRD4202X (pending meta-analysis)
    `measures:` pooled fracture events across deficiency-stratified trials
    `status:` unknown — protocol registered, no timeline posted
    `expected_report_date:` unknown

  **Update rules:**       <!-- if_outcome → direction + rough magnitude -->
  - If NCT01234567 finds ↓events (CI excludes null) → toward "supplement if
    deficient", **moderate** shift.
  - If NCT01234567 finds null → toward "neutral", **small** shift (confirmatory).
  - If terminated/underpowered → no update; note in gaps.

## C2 — ...

# Gaps

- Suspected crux — dose-response above 4000 IU/day — no trial stratifies dose finely
  enough to substantiate; not asserted. (`insufficient-evidence`)
- Live crux with **no** pending evidence found: interaction with baseline sun exposure /
  latitude — no registered trial isolates it. (a finding in itself)
```

### Field reference

| Field | Meaning | May be `unknown`/`insufficient-evidence`? |
|---|---|---|
| `question` | posed form + operationalization(s); primary marked, assumption stated | yes (if unoperationalizable) |
| `as_of` | generation date | no (always known) |
| `current_answer` | answer + `confidence` + one-paragraph basis | yes — `insufficient-evidence` is a valid, non-failing answer |
| `cruxes[].statement` | the sub-question | — |
| `cruxes[].why_it_moves` | sensitivity: how the answer shifts if it resolves each way | yes |
| `cruxes[].current_evidence` | summary + provenance + per-source **validity grade** | yes |
| `watch_list[].{source,measures,status,expected_report_date}` | one pending evidence op | yes, per field |
| `update_rules[]` | `if_outcome → update_direction + rough_magnitude` | yes |
| `recheck_on` | date to revisit | yes |
| `gaps[]` | what the process could not establish | — (empty is allowed; honest population is success) |

**Enums** (defined by the skill that owns each field; listed here for reference):

- `confidence`: `high | moderate | low | insufficient-evidence` — owned by **lit-review**.
- per-source validity `grade`: `low | some-concerns | high` — the risk-of-bias output of **`appraise-study`** (with `insufficient-evidence` for non-appraisable sources, and unjudgeable domains routed to `gaps[]`). The body-of-evidence certainty (GRADE High/Moderate/Low/Very-low) is a separate roll-up owned by **lit-review**.
- watch `status`: `recruiting | active | completed-unpublished | reported | terminated | withdrawn | unknown` — owned by **find-pending-evidence**; `unknown` when the registry doesn't say.

The example values above are **illustrative**, not a worked case.
