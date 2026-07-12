> Generated 2026-07-12 by /end-session at commit ffc5653.

# STATUS

## Where things stand

The **spec is complete and is the design of record.** This session turned `spec.md` from a stub into a full design and stood up the build roadmap.

The tool is a **Claude skillset** (not an app): hand Claude the skills + an open question, and it runs a 5-step pipeline — *operationalize → lit-review with validity grading → find cruxes → find pending evidence → pre-register update rules* — emitting a **crux watch-list artifact** ("here are the cruxes; watch these spaces; when they report, here's how to update"). Scope is a **question class defined by two properties** (unresolved *and* fast-moving in new study evidence), showcased on the eggs case plus similar egg-like questions — deliberately *not* the competition's COVID/black-hole cases, which fail the "live evidence incoming" property.

Key design commitments now locked: **self-containment & tool-agnosticism** (skills depend only on concepts we author + web search; tools are optional accelerators; this is also the licensing firewall), **calibrated humility** (refusal/`gaps[]` over confident error), and **no-overclaim** (VOI and Rootclaim borrowed as qualitative logic, not numeric computation). Prior art is mapped per step and a licensing audit cleared every borrow (§102(b): methods aren't copyrightable; a few verbatim/trademark don'ts recorded). Novel contribution = the one-shot seam **crux → pending evidence → pre-registered update rule**; we do *not* build the over-time updating loop (the user's larger project).

Next session is **build**: author the skills (Milestone #2), produce the eggs worked example + 2 generalizability cases, and write the ≤10-page submission. Deadline **2026-07-19**.

## Derived facts (from CLAUDE.md commands)

- Tracked `.md` files: **9** (`git ls-files '*.md' | wc -l`)
- `case-studies/`: does not exist yet (`ls case-studies/` → 0)
- Test status: no harness yet (TBD)
- Working tree at generation: `spec.md`, `decisions.md` modified (committed in this session's commit); HEAD `ffc5653` (`git status --short`, `git rev-parse --short HEAD`)
- Open Issues: **17** (#5–#21), all under Milestone #2 (`gh issue list --state open`)

## Active Milestone

**Working prototype** — https://github.com/lux-username/epistemic/milestone/2 (due 2026-07-19). 17 Issues: skills/components #5–#11, cross-cutting behaviors #12–#16, deliverables/validation #17–#21. Work them there. ("Spec complete" milestone closed; Issues #1–#4 closed as resolved by the spec.)

## Blockers / open questions

- None blocking. The build's primary correctness gate is **#19 (self-containment cold-start test)**; **#20 (drift check: skills vs spec)** should run mid-build and before submission to keep spec.md the true design of record.
- Note: GitHub stores milestone `due_on` as date-only, so #2 displays as `2026-07-19T00:00:00Z` (cosmetic).
