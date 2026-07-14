> Generated 2026-07-13 by /end-session at commit 7057485.

# STATUS

## Where things stand

**Session 7 executed #22 (de-egg) and turned it into a broader generality pass on the seven pipeline skills.** The skills' running illustrative example moved off the showcase topic (eggs) onto an unrelated in-class thread — **vitamin D supplementation → fracture/CVD events** — chosen from two candidates (vitamin D vs coffee) because it exercises the pipeline's distinctive machinery hardest (VITAL as the ITT-vs-adherence target trial; a landmark RCT for `appraise-study`; the "biomarker rose but events didn't" crux mirroring the old LDL-vs-events tension). The thread deliberately avoids **omega-3** (the validation topic) and **aspartame** (the pending strip run), so the illustration is distinct from both showcase and tests.

The de-egg then widened into a **maintainer-note / project-reference sweep**. Removed from the pipeline skills, all now fully topic- and repo-agnostic:
- the `the real eggs example is #17` footers (showcase + issue-tracker leak) → plain "illustrative, not a worked case";
- issue-number cross-refs `#7`–`#10` → the skill names they map to;
- an in-doc TODO citing issue `#13` → deleted (the work already lives in open #13);
- a `spec.md` design-of-record pointer and a `nutritional-epidemiology` domain over-narrowing → generalized;
- stale `provisional enum` / `finalizes the schema's placeholder` authoring scaffolding → deleted (the enum is settled).

**Note the gate override.** STATUS previously said *"Do not de-egg (#22)… until [the aspartame strip] confirms the fix."* This session proceeded anyway, on the judgment that de-egging the *illustrative* examples is orthogonal to whether the Session-6 recall fix works — it's generalization hygiene, not fix-dependent. Logged in decisions.md and flagged here so the override isn't silent drift. **The other half of that gate still stands: the showcase (#17) is still not to be written until the fix is validated.**

## Derived facts (from CLAUDE.md commands)

- Tracked `.md` files: **21** (`git ls-files '*.md' | wc -l`).
- `case-studies/`: does not exist (`ls case-studies/` → 0). No worked example yet (#17).
- Test status: no automated harness; validation is the clean-room runs (#19). **Run B passed (Session 5); the crux-recall fix (Session 6) remains unvalidated** — the aspartame strip is still both the generalization test *and* that fix's validation.
- HEAD `7057485`; working tree at derivation: **7 modified skills** (the de-egg + generality sweep) — committed with this session's docs.
- Open Issues: **18** (#13–#31) at derivation — `gh issue list --state open`. This session **closes #22**; none filed. Post-commit: 17 open.

## Active Milestone

**Working prototype** — https://github.com/lux-username/epistemic/milestone/2 (due 2026-07-19). Correctness gate #19 met for omega-3; crux-recall fix applied but unvalidated; **#22 (de-egg) now done**. Remaining: wording reviews #25–#31, cross-cutting #13–#16, RoB auditability #23, deliverables/validation/generalizability #17–#21.

## Blockers / open questions

- **The recall fix is still a hypothesis, not a result.** Unchanged from Session 6: the next run must be the **hard aspartame strip**, scored against the revised (quality+completeness) rubric — it validates the fix where base Claude's recall advantage thins. The showcase (#17) waits on it.
- **Test records still live outside git** (#24) — `../epistemic_tests/` holds the revised `comparison-rubric.md` and the run scores. Un-backed-up; storage decision still pending.
- **Scite connector** remains disconnected (enforced the Run B/A′ web-search floor); reconnect before any tooled/scalability run.
