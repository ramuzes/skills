---
name: write-specs
description: Write a SPEC from already-gathered requirements and save it to docs/specs/YYYY-MM-DD-<feature>-design.md (local mode) or as an AirBoard SPEC resource — a living document revised in place (AirBoard mode) — then self-review and hand it back for user review. Pure writing engine — it does not interview. Use when you have the requirements/context and need the spec document produced. Invoked by chat-to-spec (after it collects requirements) and by new-feature's Architectural path.
---

# Write-specs

The pure spec-writing engine. Given requirements (already gathered by a caller), produce a **SPEC** — one document holding both the *requirement* (the why and the what) and the *design* (the how) — self-review it, and ask the user to review.

This skill does **not** interview or collect requirements — that's the caller's job. `chat-to-spec` collects them through dialogue; `new-feature`'s Architectural path collects them through explore + clarifying questions + approaches. Take what the caller passes and write.

This repo has **no issue tracker** in local mode — the SPEC is a committed file, not an issue. In **AirBoard mode** the SPEC is a living service-side resource: the current statement of what the system promises, revised in place (`PATCH`), each revision being its own change record; PRDs (external requirements) and ISSUEs live alongside it as AirBoard resources.

> **AirBoard mode** is active for a repository iff its root `AGENTS.md` contains a section declaring AirBoard as the system of record (heading contains "AirBoard") **and** `docs/airboard.md` exists; otherwise the repo runs in **local mode** — the current file-based behavior, unchanged. In AirBoard mode, resolve **base URL**, **project key**, and **token environment-variable name** from `docs/airboard.md` — never hardcode them; everyone working on this repo reads that same config, especially the AirBoard URL. Read the token value from the repo-root `.env` and send `Authorization: Bearer $<TOKEN_VAR>`. Fetch `GET <base>/api/agents/instructions` fresh before any AirBoard operation and follow it as the machine contract; resource `GET`s return slim bodies, so pass `?fields=full` to read one. If the mode is declared but the service is unreachable, the token is missing, or `docs/airboard.md` fails to parse: report the error and stop to ask the user — never silently fall back to local mode.

## Process

1. **Use the project's domain language.** Read `CONTEXT.md` for vocabulary and skim the ADRs in the area you're touching — **AirBoard mode:** search the service for the area's domain keywords (`kind=ADR`, `fields=full` to read the bodies); **local mode:** skim the local files in `docs/adr/`. Use those terms throughout. Explore the repo for current state only if the caller hasn't already supplied enough context.

2. **Write the SPEC** using the template at `spec-template.md` (sibling file). Under **Testing decisions**, sketch the test seams — prefer existing seams, use the highest seam possible, and keep the count low (the ideal is one).
   - **Local mode:** save it to `docs/specs/YYYY-MM-DD-<feature>-design.md` (`<feature>` is a short kebab-case slug). This file is the durable design record.
   - **AirBoard mode:** SPECs are living documents. First search the service for an existing SPEC in the same domain (`kind=SPEC`) — if one exists, revise it via `PATCH`, prepending a `## Change YYYY-MM-DD — <slug>` section (see the template's revision note); if none, `POST` a new one with title `<domain> — <topic>`. When the requirement originates from a PRD, note `Ref: PRD-N` in the body. Report the resource ID (e.g. `SPEC-7`) to the user after creating or revising.

3. **Self-review the spec — always.** Look at the spec content with fresh eyes and fix issues inline:
   - **Placeholders** — any TBD / TODO / vague requirement
   - **Consistency** — do sections contradict each other?
   - **Ambiguity** — could any requirement be read two ways? Pick one.
   - **Scope** — focused enough for one implementation plan, or does it need splitting?

   For a deeper check, dispatch the reviewer subagent from `spec-document-reviewer-prompt.md` (sibling file).

4. **Ask the user to review** the spec content; wait. If they request changes, make them and re-review (step 3). **AirBoard mode:** present the resource body to the user (fetch it with `fields=full`) — the reviewed object is the spec content, wherever it lives.

## After the spec

Return to the caller. To implement, the caller (or user) invokes `writing-plans` to turn the spec into a plan, then `subagent-driven-development`.

## Files in this skill

- `spec-template.md` — the SPEC format (the canonical template for this workspace)
- `spec-document-reviewer-prompt.md` — subagent prompt for a deeper spec review
