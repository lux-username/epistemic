# epistemic — spec (design of record)

Changes only by deliberate decision. When a "maybe" in *Future direction* becomes real, it graduates out of this file into a Milestone with Issues.

## Goal

For an open, still-contested empirical question, produce a **crux watch-list**: identify the sub-questions whose resolution would most change the answer, then map each to the *ongoing or upcoming evidence-gathering* (trials, cohorts, pending publications) that will resolve it, with a **pre-registered update rule** for each plausible outcome. The deliverable is a **runnable prototype** for the Future of Life Foundation ["Epistemic Case Study Competition"](https://forum.effectivealtruism.org/posts/kP8TGXggDcaPhdjqn/lab-leaks-black-holes-and-eggs-epistemic-case-study) (deadline **2026-07-19**).

The one-line pitch: *"Here are the cruxes. Watch these spaces. Check back in six months, and if they've reported, here is how to update."*

## Scope — the question class

We do **not** target all three provided case studies. We target a **class defined by two properties**, not by subject matter:

1. **Unresolved** — the question is still genuinely contested in the literature; and
2. **Live** — new academic / study-type evidence is arriving fast, so cruxes await *imminent* new data.

Ingestible-health questions ("are eggs good or bad for you," "multivitamins," "artificial sweeteners," "red meat," "coffee") are the **most legible examples** and where the entered worked example will live — but they are illustrations of the class, not its boundary. The same workflow should apply to any unresolved-and-fast-moving empirical question: a contested drug's long-term effect, an environmental-exposure risk, a debated intervention whose big trials report soon, etc. The subject is incidental; the two properties are what qualify a question.

This class is chosen deliberately: unlike COVID-origins or LHC black holes (largely retrospective / resolved, no decisive evidence incoming), questions in this class have **cruxes that await imminent new data** — precisely the property the forward-looking workflow needs. Generalizability is argued *across this class* (shown on several examples), not across the three provided cases — a narrower but defensible and demonstrable claim.

The competition's other two cases are out of scope for the prototype; we reference them in the writeup only to mark the boundary — they fail property (2), which is exactly what our tool is *for* detecting.

## Deliverable form

A **Claude skillset** — a set of markdown skills that decompose the workflow below. The user runs it by handing Claude the skills and a question; Claude executes the whole process end to end and emits the artifact.

- **Tool-agnostic core.** The skills describe *what* to retrieve and reason about, not which tool must do it — see *Self-containment & tool-agnosticism* for the commitment and *Suggested tools* for the optional accelerators.
- This form is simultaneously a competition **"specification"** (a transparent, steerable human-AI workflow) and a **"prototype"** (it runs). It matches the project's markdown-first premise: files that instruct future Claudes.

## Requirements

Derived from the competition's judging rubric — each maps to a design commitment:

| Judge criterion | What we must show |
|---|---|
| **Practical utility** | Someone reasoning about "are eggs bad for me" gets a concretely more useful, actionable output than a web search — a dated watch-list they can revisit. |
| **Generalizability** | The *same* skillset, unchanged, produces a good artifact for ≥3 questions in the class (eggs + e.g. multivitamins + artificial sweeteners). |
| **Scalability** | Output improves with better models and more compute (deeper lit review, more cruxes, more trials surfaced) — no hand-tuning per question. |
| **Compounding** | The artifact is a **self-contained, hand-offable unit** that someone else can fold into a *larger* epistemic project — a personal knowledge base, an ongoing review, a team's shared notes. Because the update rules are pre-registered, whoever maintains that larger project knows how to update when a watched study reports. **We do not build the updating loop ourselves** — we produce an artifact designed to be incorporated by one. |

Hard constraints from the competition:

- Written discussion **≤ 10 pages** (excluding appendices). Worked examples may be larger if navigable.
- "Near-single-click install." For a skillset: drop the skills in, prompt Claude — nothing to build.
- Deliberate transparency about uncertain design choices and tradeoffs.

Non-negotiable design commitments (from our own constraints):

- **Self-containment & tool-agnosticism.** The worked example will be produced by a *fresh Claude in a folder that contains only the skills* — no prior conversation, no external notes, no memory of this design process. Therefore **everything the prototype needs must live inside the skill files**: the workflow, the appraisal rubric (the *method*, in our own words), the artifact schema, the refusal criteria, worked micro-examples. **No skill may depend on any external API or third-party tool.** The frameworks are built into the skills as reasoning procedures; specific tools/APIs are named only as *optional accelerators*, and the workflow must run to completion on **Claude's built-in web search alone**. If a skill breaks when a suggested tool is absent, it is a bug. This is the primary correctness test for the build — and it doubles as our licensing firewall: depending only on concepts we author, not on others' APIs or data, keeps us clear of third-party terms of use.
- **Calibrated humility — refusal over confident error.** The process must know what it does not know. If it lacks the evidence to identify a crux, judge a study's validity, or populate the watch-list, it must **say so explicitly and leave that cell empty/flagged** — never fabricate a trial, invent a plausible-sounding crux, or launder low-confidence into a clean answer. The failure mode we optimize *against* is confident misrepresentation; the acceptable failure mode is a well-scoped "insufficient evidence to determine this." Every stage skill specifies its own "when to refuse" condition, and the artifact schema carries explicit `unknown` / `insufficient-evidence` states.

## Workflow (the process the skills encode)

Input: an open question in the class (e.g. *"are eggs good or bad for you?"*).

0. **Operationalize.** The question as posed is usually underspecified — good/bad **for whom**, on **what outcome**, at **what dose**, over **what horizon**. The skill **proposes** these operationalizations *itself* from the literature; it must **never demand the user supply information they don't have**. Default behavior: enumerate the decision-relevant framings, pick the most-load-bearing as primary, carry the alternatives alongside, and state the assumption explicitly. Asking the user is a last resort, offered as an *option* ("if you care specifically about X, re-run noting that"), never a blocking gate.
1. **Lit review & analysis, with validity grading.** Survey the current evidence; extract key claims *with provenance*; and **assess each source's validity** — study design, risk of bias, sample size/power, replication, conflicts of interest — using an established, citable appraisal framework (see *Prior art*) rather than an ad-hoc judgment. Weight claims by assessed quality. Note the current best-estimate answer, its confidence, and where the disagreement actually lives.
2. **Identify cruxes.** Find the sub-questions that most move the answer, ranked by the *logic* of value of information (which unknown, if resolved, would most change the conclusion). We borrow VOI/EVPPI's reasoning as a **qualitative lens, not a computation** — we have no quantified decision model and claim no numeric EVPPI; "double crux" is the plain-language version of the same idea. For each: state it, say *why* it moves the answer (sensitivity: how much would resolving it shift the conclusion), and summarize the current state of evidence on it. A suspected crux the evidence can't yet substantiate goes to `gaps[]`, not into the crux list dressed up as established.
3. **Find pending evidence.** For each crux, search for **ongoing or upcoming evidence-gathering** that would resolve it: registered trials, cohorts, pending publications. Capture registry ID, what it measures, status, and **expected completion/report date**.
4. **Pre-register update rules.** For each crux × each plausible outcome of the pending evidence, write a conditional update: *"If [study X] finds [outcome A], update toward [direction] by [rough magnitude]; if [outcome B], update toward [other]."*
5. **Assemble the watch-list artifact** (schema below), including a concrete **re-check date**.

Output: the crux watch-list artifact.

## Data model — the crux watch-list artifact

The compounding unit. One per question. Markdown with a structured shape (frontmatter + sections). The narrative field list and design intent live here; the **concrete serialization is defined in the orchestrator skill** (`.claude/skills/crux-watchlist/SKILL.md` → *Artifact schema*), which is its single home per self-containment. The fields are:

- **question** — as posed, plus operationalized version(s) from step 0, with the primary framing marked and the assumption behind it stated.
- **as_of** — date the artifact was generated (everything below is relative to it).
- **current_answer** — best-estimate answer + confidence + one-paragraph basis. Confidence may legitimately be **`insufficient-evidence`**; a well-scoped "we can't yet answer this" is a valid, non-failing output.
- **cruxes[]** — each:
  - `statement` — the sub-question.
  - `why_it_moves` — sensitivity: how the overall answer changes if it resolves each way.
  - `current_evidence` — short summary with provenance **and per-source validity grade** (from step 1's appraisal framework).
- **watch_list[]** — per crux, one or more pending evidence-gathering ops:
  - `crux_ref`, `source` (registry/ID + link), `measures`, `status`, `expected_report_date`.
- **update_rules[]** — per crux: `{ if_outcome → update_direction + rough_magnitude }`.
- **recheck_on** — the date to re-run/revisit.
- **gaps[]** — first-class record of what the process *could not* establish: cruxes it suspects but can't substantiate, cruxes with **no** pending evidence found (a live crux nobody is studying — itself a valuable finding), and questions where evidence was too thin to grade. Populating this honestly is a success, not a shortfall.

Every field above may take an explicit **`unknown` / `insufficient-evidence`** value instead of a fabricated one — the schema makes "we don't know" representable so the process is never forced to invent.

Design intent: the artifact is a drop-in unit for a *larger* epistemic effort we don't build. A second person — or their personal knowledge base — can adopt it, disagree with a specific update rule or crux and edit *that cell*, or, when a watched study reports, apply the pre-registered rule to update their own picture. "Usefully pick up where another left off" is the whole point; **maintaining or re-running the watch-list over time is the user's project, not ours** (see *Out of scope* / *Future direction*).

## Architecture — skill decomposition

Proposed (subject to build):

- **`crux-watchlist`** — orchestrator. Takes a question, runs steps 0–5, emits the artifact.
- Stage skills it invokes (or inlines): `operationalize`, `lit-review`, `find-cruxes`, `find-pending-evidence`, `write-update-rules`.

Exact granularity — one fat skill vs. several composable ones — is a build decision, recorded in `decisions.md` when made. Bias toward composable skills so individual stages can be reused and improved independently (compounding applies to the *tooling*, not just the artifacts).

## Suggested tools (optional accelerators — never dependencies)

The **method** is the spine, built into the skills; these tools only make executing it faster or more complete — every tool below is optional, and a skill that fails without one is a bug (see *Self-containment & tool-agnosticism*). The skills describe *what to look for* (e.g. "ongoing trials with an estimated completion date") so a cold Claude can satisfy the step through whatever access it has.

**Runtime tool detection.** The orchestrator — and each stage skill that has a retrieval step — must **check at runtime which tools are actually available** (MCP servers, connected APIs, web search) and opportunistically use the most capable relevant one for the job, degrading gracefully to web search when nothing better is present. The skills name the *role* they need filled ("a trial-registry lookup," "citation-stance data") and match it to whatever is on hand, rather than hard-coding a specific tool. Absence of a tool changes *thoroughness*, never *whether the step runs*.

- **Trial registries** — ClinicalTrials.gov, WHO ICTRP (~17 registries), PROSPERO (in-progress reviews). The *concept* they serve — look up registered ongoing/upcoming studies and their expected report dates — is what makes step 3's "check back in six months" real; these are the fastest access points to it, not a requirement.
- **Literature search/extraction** — PubMed / Europe PMC, Semantic Scholar. Speed up step 1's survey and claim extraction.
- **Scite** (MCP, already connected) — citation-context signal: is a claim supported, contrasted, or merely mentioned by citing work.
- **Floor:** Claude's built-in **web search** alone — the guaranteed-available path the workflow is designed around.

**Retrieval policy: live, uncached.** Every run queries fresh so the artifact reflects current reality — a deliberate tradeoff favouring "living document" over "frozen snapshot," accepting that runs aren't reproducible and depend on whatever source is reachable at the time.

### Can it really run without the tools? (roles & fallbacks)

The tool-agnostic claim only holds if each tool's *role* survives its absence. It does — because most of these tools are structured front-doors to **public, web-indexed databases**: web search + fetch reaches the same records; the tool bought *completeness and clean structure*, not *access*.

- **Pure-method roles — never needed a tool:** operationalize (0), crux ranking via VOI/double-crux (2), conditional update rules (4), and GRADE/RoB 2 appraisal *reasoning* (1). Unaffected by tool absence.
- **Data-access roles — web-reachable:** trial registries (3) and literature retrieval (1) are public and indexed. `site:clinicaltrials.gov` searches and fetched trial records yield status + estimated completion dates; PubMed/journal pages yield the studies. **What degrades: completeness** — coverage becomes *best-effort, not systematic.*
- **Value-add processing roles — approximated by reading:** citation stance (Scite), cross-paper extraction (Elicit/Consensus), citation graph (Semantic Scholar). Claude reproduces these by reading sources directly. **What degrades: throughput** — fewer sources per run, approximate rather than exhaustive.

Two consequences, both already consistent with our commitments:
- **Disclosed, not hidden.** The artifact marks its coverage as best-effort; thin or missing coverage is a `gaps[]` entry, never a silent hole (calibrated-humility commitment).
- **This is our scalability story.** Web search is the floor; adding a tool raises recall/throughput *without changing the skills* — directly satisfying the competition's "improves with better AI and more compute" criterion. The skills are written so the *method* is fixed and the *access layer* is swappable.

## Prior art — whose lunch we borrow (with credit)

We do not reinvent evidence appraisal, forecasting, or claim-structuring. Each step leans on an established framework, cited in the skills and the writeup. Per-step primary borrows:

| Step | Borrow (with credit) | Why / how it's used |
|---|---|---|
| **0 Operationalize** | **Tetlock / Good Judgment** question-design; Registered Reports & OSF pre-registration | Turning a vague question into a *resolvable* one is exactly forecasting's question-design discipline. |
| **1 Review + validity** | **GRADE** (certainty of evidence: High/Mod/Low/Very-Low) + **RoB 2** (per-RCT risk of bias) | GRADE is the citable spine for "how good is the body of evidence"; RoB 2 grades individual trials. See appraisal note below. |
| **2 Cruxes** | **Value of Information — EVPI/EVPPI** (as a reasoning lens) + **CFAR Double Crux** (plain-language framing) | VOI is the principled account of which unknown most changes a decision; we borrow its *logic* to rank cruxes qualitatively, not its numeric computation (we have no quantified decision model). Double crux is the communicable name. |
| **3 Pending evidence** | **Trial registries** — ClinicalTrials.gov, WHO ICTRP, PROSPERO; **Trialstreamer** as the model | The registry "estimated completion date" is our report-date signal; ICTRP aggregates ~17 registries; PROSPERO reveals in-progress *reviews*. Trialstreamer already monitors PubMed+ICTRP for ongoing RCTs — the closest existing engine to step 3. We borrow the *concept* (registry lookup), not a specific API. |
| **4 Update rules** | **Metaculus conditional pairs** + **GRADE Evidence-to-Decision** + Bayesian likelihood ratios | Metaculus "if Parent resolves A, Child becomes…" *is* our update-rule structure, already operationalized with resolution discipline. |

**Appraisal-framework decision (step 1).** Primary: **GRADE + RoB 2**.

*What we borrow from RoB 2 is the mechanism, not the wording.* RoB 2 assesses one **specific result** of a trial (not the whole study) through a three-layer funnel: five bias **domains** (randomization; deviations from intended interventions; missing outcome data; outcome measurement; selection of the reported result) → ~22 factual **signaling questions** with a fixed response set (Yes / Probably Yes / Probably No / No / **No Information**) → a **deterministic algorithm** mapping the answer pattern to a domain judgment (**Low / Some concerns / High**), then aggregated to an overall judgment by a second fixed rule. Its power is the *division of labor*: the LLM answers only near-factual questions, while the risky aggregation is a fixed algorithm — which is exactly why 2025 evaluations find LLMs reliable when they answer signaling questions and let the algorithm decide, and unreliable when asked for the overall verdict directly.

The skills therefore encode: (a) the **5-domain skeleton** (a method, freely usable); (b) **our own signaling questions**, authored from understanding each domain's bias mechanism — *not paraphrases* of Cochrane's text (respects RoB 2's CC BY-NC-ND no-derivatives limit); (c) the **response schema including "No Information,"** which is a built-in humility valve mapping straight onto our `insufficient-evidence`/`gaps[]` states (an under-reported trial resolves to "Some concerns," never a fabricated "Low risk"); (d) the **factual-questions → deterministic-algorithm → judgment** pipeline. Because assessment is *per-result*, each study is appraised **relative to the specific crux it bears on**, not in the abstract — which is why appraisal lives inside the crux structure, not beside it. Step 0 also inherits RoB 2's **assignment-vs-adherence** distinction: which effect the question is even about (intention-to-treat vs per-protocol) must be declared, since it changes what counts as bias.

For bodies of evidence that are systematic *reviews*, use **AMSTAR-2 / ROBIS**, flagged as lower-confidence (LLM–expert agreement ~58–70%). Provenance QA (does a source actually back the extracted claim?) borrows the **SemanticCite** four-class model (Supported / Partially / Unsupported / Uncertain).

**The living-evidence lineage.** The watch-list concept's intellectual home is **Cochrane Living Systematic Reviews / living evidence surveillance**. We cite it — *and* cite its documented failure mode (a 2025 study found half of Cochrane's COVID living reviews were never actually updated). Sustaining continuous updating is the exact gap a cheap, re-runnable LLM skill targets; that's our reason-to-exist, stated plainly.

**Honesty borrows.** From **SCORE / repliCATS**: treat the *claim* as the atomic unit with a credibility grade attached — but cite their sobering result that automated credibility-scoring was **not reliably better than chance**, so we do not over-promise LLM validity scoring (reinforces the calibrated-humility commitment). From **Rootclaim**: the *idea* of weighting each piece of evidence by the direction and strength it points — the qualitative basis for step 4's "update toward X by rough magnitude," **not** the full numeric likelihood-ratio → posterior math (which we don't claim to do). Cited *with* its public lab-leak-debate loss as a cautionary tale about garbage-in likelihoods.

**Literature-access assistants** (optional, step 1 discovery/extraction): **Semantic Scholar API** (200M+ papers, free — the most borrowable infrastructure layer), **Elicit** (structured extraction tables), **Consensus** (yes/no + journal-quality triage), **Scite** (support/contrast/mention citation context — already connected via MCP), **Undermind** (agentic deep search). Recurring 2025 finding to honor in the design: **hybrid AI+human workflows beat fully-automated ones** — the skills keep a human in the steering loop.

### Sources

Every borrow above is credited here; the skills and writeup carry the same citations inline.

- GRADE / GRADEpro — https://www.gradepro.org/ · Evidence-to-Decision — https://pmc.ncbi.nlm.nih.gov/articles/PMC4946225/
- Cochrane RoB 2 (LLM automation & robvis/ROBoto2) — https://arxiv.org/html/2511.03048v1 · LLM–RoB2 evaluation — https://www.jmir.org/2025/1/e70450/PDF
- AMSTAR-2 / ROBIS (LLM agreement study) — https://academic.oup.com/jamia/article-abstract/32/9/1471/8206313
- Value of Information / EVPI / EVPPI — https://en.wikipedia.org/wiki/Expected_value_of_perfect_information · empirical use — https://pubmed.ncbi.nlm.nih.gov/19602213/
- CFAR Double Crux — https://www.lesswrong.com/posts/exa5kmvopeRyfJgCy/double-crux-a-strategy-for-resolving-disagreement
- Tetlock / Good Judgment — https://www.founderspledge.com/research/prof-philip-tetlocks-forecasting-research · question-design — https://arxiv.org/pdf/2303.18006
- Metaculus conditional pairs — https://www.metaculus.com/faq/ · forecasting-tools — https://github.com/Metaculus/forecasting-tools
- Trialstreamer — https://pmc.ncbi.nlm.nih.gov/articles/PMC7727361/
- Cochrane Living Systematic Reviews / living evidence — https://onlinelibrary.wiley.com/doi/full/10.1002/cesm.70024 · gap maps — https://www.cochrane.org/about-us/news/piloting-living-evidence-gap-maps-address-global-health-priorities
- MAGICapp — https://magicevidence.org/magicapp/
- SCORE — https://www.cos.io/score · repliCATS — https://replicats.research.unimelb.edu.au/
- Rootclaim — https://www.rootclaim.com/how-rootclaim-works · critique — https://www.astralcodexten.com/p/practically-a-book-review-rootclaim
- SemanticCite — https://arxiv.org/abs/2511.16198
- SR-automation (hybrid AI+human finding) — https://link.springer.com/article/10.1186/s13643-024-02592-3
- Micro/nanopublications — https://arxiv.org/pdf/1305.3506
- ClinicalTrials.gov API — https://clinicaltrials.gov/data-api/api · WHO ICTRP — https://www.who.int/tools/clinical-trials-registry-platform
- Semantic Scholar API — https://www.semanticscholar.org/product/api · AI-assistant comparison — https://www.iatrox.com/blog/best-ai-tools-medical-research-2026-elicit-consensus-semantic-scholar-perplexity

### Permissions & attribution (licensing audit — cleared)

A permission audit confirmed every borrow is usable for a public, open-source entry. Governing principle: **17 U.S.C. § 102(b)** — copyright never covers a "procedure, process, system, method of operation, [or] concept," only its specific *expression*. We describe-and-apply methods in our own words, so the method-borrows are clear. Operative rules the skills must follow:

- **Required citations** (include a short "Sources & methods" block in the skills): GRADE Working Group; **RoB 2** — Sterne et al., *BMJ* 2019;366:l4898; **AMSTAR-2** — Shea et al., *BMJ* 2017;358:j4008 (attribution *required*, CC BY); **ROBIS** — Whiting et al., *J Clin Epidemiol* 2016;69:225-234; **Double Crux** — CFAR/LessWrong; **forecasting** — Tetlock & Gardner, *Superforecasting* (2015); **VOI** — Raiffa & Schlaifer (1961) / Claxton et al.
- **Do NOT reproduce verbatim:** RoB 2 signaling-question text (CC BY-**NC-ND** → describe only, never paste), ROBIS items, and substantial text from the Double Crux post. *Exception:* AMSTAR-2 is CC BY — verbatim allowed with citation.
- **Trademark / naming:** never label our tool, its users, or its output "**Superforecaster/Superforecasting**" (registered marks of Good Judgment Inc. — use "calibrated forecasting"); don't imply **Cochrane** endorsement or use it as a badge; don't brand our adaptation as "**GRADE**" if we deviate from the method (Working Group request). For any named third-party product: name only, no logos, with a one-line "names are trademarks of their owners; mention is not endorsement."
- **Tool-specific (only relevant when a user opts into a tool — we don't depend on any):** Trialstreamer *data* is CC0 but its *code* is unlicensed (don't copy code); Scite's MCP and Elicit/Consensus/Undermind APIs are paid and some carry restrictive terms; if a user does invoke a public API, surface its courtesy line ("Courtesy of the U.S. National Library of Medicine" for ClinicalTrials.gov/PubMed, "WHO ICTRP" + processing date, "The Semantic Scholar Open Data Platform"). Because the skills never *require* these, none of their terms bind the prototype itself.
- **Residual unverified (non-blocking):** PROSPERO's exact terms and Good Judgment's full terms text couldn't be fetched; neither is depended on (we cite Tetlock's book, not the platform; we don't require PROSPERO), so neither blocks us.

### Positioning — our novel seam

The prior-art scan found **no single project spanning crux → pending evidence → pre-registered update rule.** The pieces sit in silos: living reviews + Trialstreamer own steps 1+3 but have no cruxes and no pre-committed update rules (they re-synthesize instead); Metaculus owns step 4's structure but isn't wired to a graded evidence base; SCORE owns claim credibility but not cruxes-over-a-question. Our defensible contribution is the **seam that connects them in a single pass**: VOI/EVPPI-style crux ranking → registry-driven pending-evidence tracking → pre-committed conditional updates against a GRADE-appraised evidence base. (This is the one-shot analysis pipeline — *not* an over-time updating loop, which we deliberately leave to the user; see *Out of scope*.) Everything else is explicitly borrowed-with-credit. The writeup leads with this positioning so judges see contribution, not reinvention.

## Out of scope

- COVID-origins and LHC-black-hole case studies (wrong fit for a forward-looking tool).
- Retrospective-only questions with no incoming evidence.
- A standalone app / Python pipeline / hosted service — the deliverable is skills, not software to deploy.
- **The updating loop itself.** We produce a one-shot artifact with pre-registered update rules; *maintaining, re-running, or auto-updating* a watch-list over time — the "living database" — is the user's larger project, which our artifact is designed to slot into but which we do not build.
- Building or hosting our own literature index or corpus; retrieval rides on Claude's built-in web search (plus whatever tools the user already has).

## Future direction / out of scope (for now)

Aspirational, not-yet-decided. Lives only here.

- A standalone runnable pipeline (non-skill) if the skillset proves the concept.
- Automated re-run scheduling ("email me when the watch-list has updates").
- Extending beyond ingestible-health to other forward-resolvable question classes.
- Caching/reproducibility layer if judges or users need frozen runs.
