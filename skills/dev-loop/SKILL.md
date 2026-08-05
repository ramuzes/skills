---
name: dev-loop
description: The canonical develop→ship loop for this workspace. Use at the start of any non-trivial feature, refactor, or fix to pick the right skill at each phase and keep design docs in their place.
---

# Dev loop

This skill routes you to the right sibling skill at each phase so you never wonder "which skill?". All design docs live under `docs/` by type; `CONTEXT.md` lives at the repo root.

## System of record

| Doc | Location | Producer | Lifecycle |
|---|---|---|---|
| Glossary | `CONTEXT.md` (root) | `domain-modeling` | persistent, curated |
| Decisions / guidelines | `docs/adr/` | `domain-modeling` | persistent, curated |
| **SPEC** — why+what (the requirement) + how (the design) | `docs/spec/<feature>.md` | `to-spec` | persistent |
| **PLAN** — implementation plan | `docs/plan/YYYY-MM-DD-<feature>.md` | `writing-plans` | persistent (committed) |
| sdd workspace (ledger, briefs, review packages) | `.scratch/sdd/<plan>/` | `subagent-driven-development` | scratch — gitignored, deleted on merge |

**No PRD doc** — requirements live inside each SPEC. **No issue tracker yet** — `to-tickets` is deferred; specs and plans are files, not issues.

**On merge:** delete the `.scratch/sdd/` workspace (git history is the record now); promote any non-obvious decision to a 5-line ADR; update `CONTEXT.md` if new terms crystallized. The SPEC and PLAN stay — they're the durable design record.

## Session rules

- **Start every design/implementation session on a new branch.** All work for the feature stays on that one branch.
- **Worktree is opt-in** — only when you explicitly ask for one. Location: `.worktrees/` (gitignored). The default is a plain branch.
- **Code-review after every task**, whether or not you're using `subagent-driven-development`.
- **Final review checks spec compliance** — was the SPEC/PLAN implemented correctly? — not code style.

## Phase → skill

| Phase | Skill |
|---|---|
| Explore | `research` → `grill-with-docs` |
| Spec | `to-spec` → writes `docs/spec/<feature>.md` |
| Plan | `writing-plans` → writes `docs/plan/YYYY-MM-DD-<feature>.md` |
| Isolate | a new branch (worktree only on request) |
| Execute | `implement`, or `subagent-driven-development` for multi-task refactors |
| Build quality | `test-driven-development` |
| Verify | `verification-before-completion` |
| Review | code-review after each task; final review = spec compliance |
| Finish | `finishing-a-development-branch` → delete `.scratch/sdd/`, write any ADR, update `CONTEXT.md` |

## When skills overlap, pick one

- TDD → `test-driven-development`
- Debug → `systematic-debugging`
- Explore intent → `grill-with-docs` (it reads CONTEXT/ADRs)
- Execute small → `implement`; execute big → `subagent-driven-development`

If a phase has no sibling skill above, do the obvious thing — don't invent ceremony.
