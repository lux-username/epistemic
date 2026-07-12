# CLAUDE.md — how to work here

Read at the start of every session. This is "how we work here," not what the project is (that's spec.md).

## Project

epistemic — a prototype tool that, for an open question, identifies the *crux* (the sub-question that most changes the answer) and surfaces promising research that may imminently resolve it. For the Future of Life Foundation "Epistemic Case Study Competition."

## Stack

Mostly markdown files that instruct future Claudes (prompts, playbooks, worked case studies). The rest of the stack and tooling is **not yet chosen** — decide it deliberately and record the choice in `decisions.md` when you do.

## Key commands

| Action | Command |
|---|---|
| Build | _TBD — no build step yet_ |
| Test | _TBD — no test harness yet_ |
| Run | _TBD — no runnable entrypoint yet_ |

## Derived Facts — read by /end-session and /weekly-reconciliation

Every fact below is obtained by **running the command, never by recall**. If you catch yourself typing one of these from memory into any doc, stop and run the command instead. Add a row whenever a new kind of fact starts appearing in docs — an unrepresented fact here is a future drift.

Nothing is settled enough to derive yet. As the tool takes shape, add rows here **before** the fact starts appearing in STATUS.md. Likely early candidates for a markdown-driven project:

| Fact | Command |
|---|---|
| Markdown file count | `git ls-files '*.md' \| wc -l` |
| Case studies present | `ls case-studies/ 2>/dev/null \| wc -l` _(once that dir exists)_ |
| Test status | _TBD — add the command when a test harness exists_ |

## Rules

- **Every fact has exactly one home.** Reference or derive; never copy into a second place.
- Current state → `STATUS.md` (overwritten). History → `journal/` (append). Rationale → `decisions.md` (append). Open tasks → GitHub Issues. Roadmap → Milestones. Design → `spec.md`. Repo layout → `MAP.md` (tree derived by command; annotations curated; refreshed by /weekly-reconciliation).
- **Plans never live only in the journal.** If it's open work, it's an Issue.
- Session start: read `STATUS.md` (check its stamp date), then `gh issue list --state open`.
- Session end: run **/end-session**. Always — a skipped run is how STATUS.md starts lying.
