> Generated 2026-07-12 by /end-session at commit ee1c67c.

# STATUS

## Where things stand

The **spec is complete and is the design of record**; the build roadmap is stood up. No product built yet — next session is the build.

The tool is a **Claude skillset** (not an app): hand Claude the skills + an open question, and it runs a 5-step pipeline — *operationalize → lit-review with validity grading → find cruxes → find pending evidence → pre-register update rules* — emitting a **crux watch-list artifact** ("here are the cruxes; watch these spaces; when they report, here's how to update"). Scope is a **question class defined by two properties** (unresolved *and* fast-moving in new study evidence), showcased on the eggs case — deliberately *not* the competition's COVID/black-hole cases, which fail the "live evidence incoming" property.

Design commitments locked in `spec.md`: **self-containment & tool-agnosticism** (skills depend only on concepts we author + web search; tools are optional accelerators; also the licensing firewall), **calibrated humility** (refusal/`gaps[]` over confident error), **no-overclaim** (VOI and Rootclaim borrowed as qualitative logic, not numeric computation). Prior art mapped per step; licensing audit cleared every borrow. Novel contribution = the one-shot seam **crux → pending evidence → pre-registered update rule**; the over-time updating loop is out of scope (the user's larger project).

Workflow note: the `end-session` skill now **always pushes** after committing (added this session).

Next session is **build** (Milestone #2): author the skills, produce the eggs worked example + 2 generalizability cases, write the ≤10-page submission. Deadline **2026-07-19**.

## Derived facts (from CLAUDE.md commands)

- Tracked `.md` files: **10** (`git ls-files '*.md' | wc -l`)
- `case-studies/`: does not exist yet (`ls case-studies/` → 0)
- Test status: no harness yet (TBD)
- Working tree: clean, in sync with `origin/main`; HEAD `ee1c67c` (`git status --short`, `git rev-parse --short HEAD`)
- Open Issues: **17** (#5–#21), all under Milestone #2 (`gh issue list --state open`)

## Active Milestone

**Working prototype** — https://github.com/lux-username/epistemic/milestone/2 (due 2026-07-19). 17 Issues: skills/components #5–#11, cross-cutting behaviors #12–#16, deliverables/validation #17–#21. ("Spec complete" milestone closed; Issues #1–#4 closed as resolved by the spec.)

## Blockers / open questions

- None blocking. The build's primary correctness gate is **#19 (self-containment cold-start test)**; **#20 (drift check: skills vs spec)** should run mid-build and before submission to keep spec.md the true design of record.
- Note: GitHub stores milestone `due_on` as date-only, so #2 displays as `2026-07-19T00:00:00Z` (cosmetic).
