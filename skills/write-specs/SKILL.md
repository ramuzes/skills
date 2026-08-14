---
name: write-specs
description: Write a SPEC from already-gathered requirements and save it to docs/specs/YYYY-MM-DD-<feature>-design.md, then self-review and hand it back for user review. Pure writing engine — it does not interview. Use when you have the requirements/context and need the spec document produced. Invoked by chat-to-spec (after it collects requirements) and by new-feature's Architectural path.
---

# Write-specs

The pure spec-writing engine. Given requirements (already gathered by a caller), produce a **SPEC** — one document holding both the *requirement* (the why and the what) and the *design* (the how) — self-review it, and ask the user to review.

This skill does **not** interview or collect requirements — that's the caller's job. `chat-to-spec` collects them through dialogue; `new-feature`'s Architectural path collects them through explore + clarifying questions + approaches. Take what the caller passes and write.

This repo has **no issue tracker** — the SPEC is a committed file, not an issue.

## Process

1. **Use the project's domain language.** Read `CONTEXT.md` for vocabulary and skim the ADRs in the area you're touching; use those terms throughout. Explore the repo for current state only if the caller hasn't already supplied enough context.

2. **Write the SPEC** to `docs/specs/YYYY-MM-DD-<feature>-design.md` using the template at `spec-template.md` (sibling file). `<feature>` is a short kebab-case slug. Under **Testing decisions**, sketch the test seams — prefer existing seams, use the highest seam possible, and keep the count low (the ideal is one). This file is the durable design record.

3. **Self-review the spec — always.** Look at it with fresh eyes and fix issues inline:
   - **Placeholders** — any TBD / TODO / vague requirement
   - **Consistency** — do sections contradict each other?
   - **Ambiguity** — could any requirement be read two ways? Pick one.
   - **Scope** — focused enough for one implementation plan, or does it need splitting?

   For a deeper check, dispatch the reviewer subagent from `spec-document-reviewer-prompt.md` (sibling file).

4. **Ask the user to review** the written spec; wait. If they request changes, make them and re-review (step 3).

## After the spec

Return to the caller. To implement, the caller (or user) invokes `writing-plans` to turn the spec into a plan, then `subagent-driven-development`.

## Files in this skill

- `spec-template.md` — the SPEC format (the canonical template for this workspace)
- `spec-document-reviewer-prompt.md` — subagent prompt for a deeper spec review
