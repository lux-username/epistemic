# Decisions — append-only

"Chose X over Y because Z." Dated, newest at the bottom. Rationale lives here so it stops hiding in changelog bullets and commit messages.

<!-- Format:

## YYYY-MM-DD — Chose X over Y

Because Z. (Link the journal entry or issue if the context helps.)

-->

## 2026-07-12 — Chose a public repo over private

Competition entry with no secrets in the tree and no reason to hide the work; public is simpler and the default for a portfolio/competition artifact. (journal/2026-07-12-1.md)

## 2026-07-12 — Framed the first Milestone as "Spec complete" rather than a build target

Stack, commands, and the crux-identification approach are all undecided, so committing to a build deliverable now would be guessing. Made the first milestone about turning spec.md into the design of record (Issues #1–#4) instead. (journal/2026-07-12-1.md)

## 2026-07-12 — Scoped to the "egg class" of ingestible-health questions, not all three competition cases

The tool's distinctive move is forward-looking (crux → pending evidence → pre-registered update rule). COVID-origins and LHC black holes are retrospective/resolved — no decisive evidence is incoming, so the "watch these spaces" step has nothing to grab. Eggs, multivitamins, artificial sweeteners, etc. have live cruxes with registered ongoing trials. Chose to argue generalizability *across this class* rather than across the three provided cases — narrower but demonstrable.

## 2026-07-12 — Deliverable is a Claude skillset, not a Python pipeline

Considered "prototype tool" as a standalone LLM pipeline (Python + Anthropic SDK + PubMed/ClinicalTrials.gov). Chose instead a set of Claude skills: hand Claude the skills + a question and it runs the process end to end. It is both a competition "specification" (transparent, steerable workflow) and a runnable "prototype," needs no install, and matches the project's markdown-first premise. Core is tool-agnostic; tools (ClinicalTrials.gov, PubMed/Europe PMC, Scite) are *suggested* enhancers, with Claude web search as the floor.

## 2026-07-12 — Live, uncached retrieval

Every run queries literature/trial sources fresh so the watch-list reflects current reality — favoring the "living document" selling point over reproducibility. Accepted tradeoff: non-reproducible runs and exposure to API downtime during judging. A caching layer is parked in *Future direction*.

## 2026-07-12 — ClinicalTrials.gov API as the spine of the "pending evidence" step

Its machine-readable list of ongoing/upcoming trials with expected completion dates turns "check back in six months" from a guess into a real query. This data source is what makes the forward-looking step feasible and is the reason the egg-class scope is the right fit.

## 2026-07-12 — Scope defined by two properties, not by subject (nutrition is just the example)

The question class is anything **unresolved** *and* **fast-moving in new study evidence** — not "nutrition." Nutrition/ingestible-health is the most legible example set and where the worked example lives, but the workflow targets any question with live, imminently-resolving cruxes. This keeps generalizability honest while still giving a concrete demo.

## 2026-07-12 — Operationalize step must never demand info the user lacks

The skill proposes operationalizations from the literature itself, picks the load-bearing framing, states its assumption, and only *offers* user input as an option — never gates on it. Guards against a tool that interrogates users about outcomes/doses/subgroups they can't be expected to know.

## 2026-07-12 — Two non-negotiable build commitments: self-containment and calibrated humility

(1) The worked example is produced by a fresh Claude in a folder containing only the skills, so everything the prototype needs must live in the skill files — this is the build's primary correctness test and moots the skill-granularity question (whatever we pick must pass a cold-start run). (2) The process must know what it doesn't know: fail-state is explicit refusal / `insufficient-evidence`, never fabricated cruxes or trials. Schema carries first-class `unknown`/`insufficient-evidence` values and a `gaps[]` list — including "a live crux nobody is studying," which the humility requirement turned into a valuable output rather than a hole.

## 2026-07-12 — Prior-art borrows chosen per step (GRADE+RoB2, VOI/EVPPI, Metaculus conditional pairs)

From a prior-art scan. Step 1 validity uses **GRADE + RoB 2** (RoB 2 chosen as the most LLM-automatable appraisal tool — a signaling-question decision tree applied deterministically; AMSTAR-2/ROBIS for reviews, flagged lower-confidence). Step 2 cruxes are framed as **Value of Information / EVPPI** (the quantitative formalization of "crux") plus double-crux as the plain-language framing. Step 4 update rules borrow **Metaculus conditional pairs** + GRADE Evidence-to-Decision + Bayesian likelihood ratios. Living Systematic Reviews are cited as the watch-list's lineage, with their sustainability failure (half of Cochrane's COVID living reviews never updated) as our reason-to-exist. Rationale: don't reinvent evidence appraisal/forecasting; borrow-with-credit and position against them.

## 2026-07-12 — Skills depend on concepts/frameworks only, never on an external API or tool

Tool-agnosticism promoted to a hard rule and merged with self-containment: no skill may *depend* on ClinicalTrials.gov, Semantic Scholar, Scite, or any third-party API/tool. The frameworks (GRADE, RoB 2, VOI, etc.) are built into the skills as reasoning procedures in our own words; specific tools are named only as optional accelerators, and the workflow must run to completion on Claude's built-in web search alone. Rationale: (1) portability — the worked example runs in a bare folder; (2) it is our licensing firewall — depending only on concepts we author keeps us clear of third-party terms of use (Borrow Type B). Reframed ClinicalTrials.gov from "spine of step 3" to "fastest access point to the registry-lookup concept, not a requirement."

## 2026-07-12 — Licensing audit cleared all borrows; encoded the operative rules in the skills

Audited every prior-art borrow for a public open-source entry. Nothing disqualifying. Anchor: 17 U.S.C. § 102(b) — methods/procedures aren't copyrightable, only their specific expression, so describe-and-apply in our own words is clear. Operative constraints now in spec.md *Permissions & attribution*: (1) required citations incl. AMSTAR-2 attribution which is mandatory (CC BY); (2) do NOT paste RoB 2 signaling-question text (CC BY-NC-ND) or ROBIS items or substantial Double Crux post text verbatim — AMSTAR-2 verbatim is the one exception; (3) trademark cautions — never label output "Superforecaster/Superforecasting" (Good Judgment ®; use "calibrated forecasting"), don't imply Cochrane endorsement, don't call a deviating adaptation "GRADE"; (4) API courtesy lines apply only if a user opts into a tool — since the skills depend on no API, no third-party terms bind the prototype. Reinforces the tool-agnostic decision: not depending on APIs is also the licensing firewall.

## 2026-07-12 — Skills detect available tools at runtime and match them to roles

The orchestrator and every retrieval-bearing stage skill check at runtime which tools are actually present (MCP servers, connected APIs, web search) and use the most capable relevant one, degrading to web search. Skills name the *role* they need ("trial-registry lookup," "citation-stance data") rather than hard-coding a tool. Keeps tool-agnosticism real while still exploiting tools when a user has them — tool absence changes thoroughness, never whether a step runs.

## 2026-07-12 — Adapt RoB 2 by borrowing its mechanism, not rephrasing its questions

Decided the step-1 validity appraisal reuses RoB 2's *mechanism* — 5-domain skeleton, the Yes/Probably-Yes/Probably-No/No/No-Information response schema, and the factual-signaling-questions → deterministic-algorithm → Low/Some-concerns/High pipeline — but authors our **own** signaling questions from an understanding of each domain's bias mechanism rather than paraphrasing Cochrane's (which also respects the CC BY-NC-ND no-derivatives limit). Two payoffs beyond legality: RoB 2's "No Information" response is a ready-made humility valve that maps onto our `insufficient-evidence`/`gaps[]` states; and because RoB 2 grades a *specific result*, appraisal lives inside the crux structure (each study judged relative to the crux it bears on), and step 0 inherits the assignment-vs-adherence (ITT vs per-protocol) distinction. Rationale: the value of RoB 2 is the division of labor (LLM answers factual questions, fixed algorithm aggregates), not the specific wording — so rephrasing would copy the wrong thing.

## 2026-07-12 — Compounding = incorporable into a larger project; we do NOT build the updating loop

Editor pass caught the spec selling the artifact as "re-runnable, mechanically re-checked" without any workflow step that consumes a prior artifact and applies its update rules — an over-promise. Resolved by scoping *down*, not building up: our deliverable is a **one-shot artifact** with pre-registered update rules, designed to be **folded into someone else's larger epistemic project** (a personal knowledge base, an ongoing review). Maintaining/re-running/auto-updating a watch-list over time is explicitly out of scope (the "living database" is the user's project). This keeps the compounding claim honest and keeps the 7-day build tractable.

## 2026-07-12 — Walk back rigor we don't have (VOI and Rootclaim borrows are qualitative)

Editor pass flagged two overclaims that brushed our own calibrated-humility rule. Fixed: (1) VOI/EVPPI is borrowed as a **qualitative reasoning lens** to rank cruxes, not a numeric computation — we have no quantified decision model and claim no EVPPI number. (2) Rootclaim's likelihood-ratio→posterior is borrowed as the **idea** of direction/strength weighting behind step 4's "update toward X by rough magnitude," not the full numeric LR math. Principle: never imply more rigor than the tool delivers. Also fixed a stale contradiction ("we query existing public APIs" in Out of scope) left over from before the tool-agnostic pivot, plus redundancy/polish.

## 2026-07-12 — The defensible contribution is the loop, not any single step

The scan found no existing project spanning crux → pending evidence → pre-registered update rule; the pieces live in silos (living reviews own 1+3, Metaculus owns 4's structure, SCORE owns claim credibility, VOI is unused glue). Our novelty is connecting them: VOI-ranked cruxes → registry-driven pending-evidence tracking → pre-committed conditional updates against a GRADE-appraised base. The writeup leads with this positioning so judges see contribution, not reinvention.
