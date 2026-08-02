# ramuz-skills

Personal, curated Claude Code skills for work on the Perseo quant stack. Drawn from two upstream skill sets, trimmed to the subset that earns a place, and maintained here under my own control.

## Sources

Copied and lightly curated from:

- **superpowers** — `anthropics/claude-plugins-official` (plugin `superpowers`). Execution, isolation, verification, TDD, debugging, planning.
- **mattpocock-skills** — `github.com/mattpocock/skills` (MIT). Research, grilling, spec/ticket flows, implementation, domain modeling, wayfinding.

`dev-loop` is original to this repo. Re-sync a skill by copying the current upstream `SKILL.md` (and any companion files / `agents/` / `scripts/`) over the local copy.

## Skills

**Original**
- `dev-loop` — the canonical develop→ship loop; routes to the right skill at each phase and enforces the "plans are branch-scoped scratch" rule.

**From superpowers** (execution + discipline)
- `test-driven-development`, `systematic-debugging`, `writing-plans`, `subagent-driven-development`, `using-git-worktrees`, `verification-before-completion`, `finishing-a-development-branch`

**From mattpocock-skills** (framing + tracking)
- `research`, `grill-with-docs`, `to-spec`, `to-tickets`, `implement`, `domain-modeling`, `wayfinder`

## Install

```
/plugin marketplace add /home/ramuz/quant/claude-skills   # or your git remote
/plugin install ramuz-skills
/reload-plugins
```

Install at **user scope** so it follows you across repos. Then uninstall the upstream plugins to avoid duplicate matchers:

```
/plugin uninstall superpowers
/plugin uninstall mattpocock-skills
```

## The loop, one line

`research → grill-with-docs → to-spec → to-tickets → using-git-worktrees + subagent-driven-development (TDD inside) → verification-before-completion → /code-review → finishing-a-development-branch → (delete plan scratch, write ADR, close milestone)`.

See `skills/dev-loop/SKILL.md`.

## Maintain

Edit a skill → commit → push. Other machines: `git pull` + `/reload-plugins`.
