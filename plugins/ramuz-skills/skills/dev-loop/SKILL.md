---
name: dev-loop
description: The canonical develop→ship loop for this workspace. Use at the start of any non-trivial feature, refactor, or fix to pick the right skill at each phase and keep planning artifacts from accumulating.
---

# Dev loop

One system of record; everything else is scratch that condenses. This skill routes you to the right sibling skill at each phase so you never wonder "which skill?".

## System of record (what persists)

| Artifact | Home | Lifecycle |
|---|---|---|
| Planned / done work | GitLab issues + milestones | closes → auto-archived |
| Decisions worth remembering | `docs/adr/` | persists, curated |
| Domain language | `CONTEXT.md` | persists, curated via `domain-modeling` |
| Living PRD (rare) | `docs/prd/` | persists only if referenced long-term |
| Step-by-step plan | **inside the worktree/branch** | **deleted on merge**, distilled to an ADR |

**Bloat rule:** a plan doc is scratch tied to a branch. On merge — delete it, promote any non-obvious decision to a 5-line ADR, close the milestone. Only ADRs + `CONTEXT.md` accumulate, slowly.

## Phase → skill

| Phase | Normal feature | Big refactor |
|---|---|---|
| Explore | `research` → `grill-with-docs` | same |
| Spec | `to-spec` → GitLab issue (+ `docs/prd/X.md` only if substantial) | + `writing-plans` for the step plan (scratch, in worktree) |
| Break down | `to-tickets` → issues under a milestone | issues + plan linked from milestone |
| Isolate | a branch is enough | `using-git-worktrees` |
| Execute | `implement` | `subagent-driven-development` |
| Build quality | `test-driven-development` | same |
| Verify | `verification-before-completion` | same |
| Review | the repo's `/code-review` | same |
| Finish | merge → close milestone | `finishing-a-development-branch` → delete scratch, write ADR, update `CONTEXT.md` |
| Many threads | `wayfinder` | same |

## When skills overlap, pick one

- TDD → `test-driven-development`
- Debug → `systematic-debugging`
- Explore intent → `grill-with-docs` (it reads CONTEXT/ADRs)
- Execute small → `implement`; execute big → `subagent-driven-development`

If a phase has no sibling skill above, do the obvious thing — don't invent ceremony.
