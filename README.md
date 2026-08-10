# ramuz-skills

Personal, curated Claude Code skills for work on the Perseo quant stack. Drawn from two upstream skill sets, trimmed to the subset that earns a place, and maintained here under my own control.

## Skills

**From superpowers** (execution + discipline)
- `test-driven-development`, `systematic-debugging`, `writing-plans`, `subagent-driven-development`, `using-git-worktrees`, `verification-before-completion`, `finishing-a-development-branch`

**From mattpocock-skills** (framing + tracking)
- `research`, `grill-with-docs`, `to-spec`, `to-tickets`, `implement`, `domain-modeling`, `wayfinder`

## Install

### Claude Code

```
/plugin marketplace add https://github.com/ramuzes/skills
/plugin install ramuz-skills
/reload-plugins
```

### Kimi Code

```
/plugins marketplace add https://github.com/ramuzes/skills
/plugins install ramuz-skills
/new
```

Kimi Code reads plugin metadata from `.kimi-plugin/plugin.json` and skill definitions from `skills/`. Start a fresh session with `/new` after install or update.

### Both

Install at **user scope** so it follows you across repos. Then uninstall the upstream plugins to avoid duplicate matchers:

```
/plugin uninstall superpowers
/plugin uninstall mattpocock-skills
```

## The loop, one line

`research → grill-with-docs → to-spec → to-tickets → using-git-worktrees + subagent-driven-development (TDD inside) → verification-before-completion → /code-review → finishing-a-development-branch → (delete plan scratch, write ADR, close milestone)`.

See `skills/new-feature/SKILL.md`.

## Maintain

Edit a skill → commit → push. Other machines: `git pull` + `/reload-plugins`.
