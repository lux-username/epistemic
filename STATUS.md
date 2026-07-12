> Generated 2026-07-12 by /end-session at commit 8853b0e.

# STATUS

## Where things stand

Day one. The project workflow skeleton is scaffolded and pushed to GitHub — README, CLAUDE.md, spec.md, decisions.md, MAP.md, STATUS.md, `journal/`, `sessions/`, and the two per-repo skills (`end-session`, `weekly-reconciliation`). No product work yet.

The **goal** is set (see `spec.md`): a prototype tool that, for an open question, identifies the *crux* — the sub-question whose resolution most changes the answer — and surfaces promising research that may imminently resolve it. For the Future of Life Foundation "Epistemic Case Study Competition."

Current thinking: the tool will be mostly markdown that instructs future Claudes (prompts, playbooks, worked case studies). **Stack, commands, and the crux-identification approach are all deliberately undecided.** The next session's real work is fleshing out `spec.md` — the competition deliverable / eval criteria, the approach, and the case-study data model.

## Derived facts (from CLAUDE.md commands)

- Tracked `.md` files: **8** (`git ls-files '*.md' | wc -l`)
- `case-studies/`: does not exist yet (`ls case-studies/` → 0)
- Test status: no harness yet (TBD)
- Working tree: clean; HEAD `8853b0e` (`git status --short`, `git rev-parse --short HEAD`)
- Open Issues: **4**, all under Spec complete (`gh issue list --state open`)

## Active Milestone

**Spec complete** — https://github.com/lux-username/epistemic/milestone/1. Four scoping Issues (#1–#4) now track the open questions below; work them there.

## Blockers / open questions

- #1 — competition deliverable format & evaluation criteria not yet captured (blocks `spec.md` Requirements).
- #2 — crux-identification approach undecided (blocks Architecture).
- #3 — case-study data model undefined (blocks Data model; would create `case-studies/`).
- #4 — whether any non-markdown tooling is needed at all is still open.
