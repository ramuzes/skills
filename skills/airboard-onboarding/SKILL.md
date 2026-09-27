---
name: airboard-onboarding
description: Check, set up, or learn a repo's AirBoard knowledge-tracking configuration — the AGENTS.md declaration, docs/airboard.md (base URL, project key, token env var), and the repo-root .env token. Use when the user asks whether a repo is (or should be) in AirBoard mode, or when onboarding a developer or agent onto an AirBoard-mode repo.
disable-model-invocation: true
---

# AirBoard Onboarding

User-invoked utility for AirBoard mode — never auto-invoked. AirBoard is the recommended knowledge tracker for this skill set: ADR / PRD / SPEC / PLAN / ISSUE live as service-side resources with server-assigned IDs, append-only revision history, and trigram full-text search. The workflow skills are dual-mode: they use AirBoard when the repo declares it, local files otherwise. This skill is the on-ramp — one of three jobs:

1. **Status check** — determine the config status of the current repo (default when invoked without a specific ask).
2. **Onboard this repo** — set up AirBoard mode for a repo that wants it.
3. **Onboard a person/agent** — orient someone who just started working on an AirBoard-mode repo.

## The probe

> **AirBoard mode** is active for a repository iff its root `AGENTS.md` contains a section declaring AirBoard as the system of record (heading contains "AirBoard") **and** `docs/airboard.md` exists. Otherwise the repo runs in **local mode** — knowledge lives as local files (`docs/adr/`, `docs/specs/`, `docs/plan/`, `.scratch/`), and the workflow skills behave exactly as they always have.

Both halves are required on purpose: the `AGENTS.md` section is the declaration humans and agents read; `docs/airboard.md` is the machine-readable config the skills parse. Once a repo chooses AirBoard, **everyone working on it reads the same config** — especially the AirBoard URL — so no session ever guesses or hardcodes it.

In AirBoard mode, the three config values are: **base URL**, **project key**, and **token environment-variable name** (token value lives in the repo-root `.env`, sent as `Authorization: Bearer $<TOKEN_VAR>`; `.env` must be gitignored). Before any AirBoard operation, fetch `GET <base>/api/agents/instructions` fresh — it is the service's self-documenting machine contract. Resource `GET`s return slim bodies; pass `?fields=full` to read one.

## Mode 1 — Status check (default)

Run these checks in order and report the results as a table with a verdict. Do not "fix" anything in this mode without being asked.

1. **Declaration** — does the root `AGENTS.md` contain a section whose heading contains "AirBoard" declaring it the system of record for ADR / PRD / SPEC / PLAN / ISSUE?
2. **Config file** — does `docs/airboard.md` exist and parse all three elements (base URL, project key, token environment-variable name)?
3. **Token** — does the repo-root `.env` define that variable? Is `.env` gitignored (a token must never be committed)?
4. **Service** — with the token, does `GET <base>/api/agents/instructions` respond? Follow that document as the contract.
5. **Project** — does the project key resolve (e.g. a trivial search or resources list against it)?

Verdicts:

- **All checks pass → AirBoard mode active.** Report base URL, project key, and token variable name; remind the user that new sessions on this repo read `docs/airboard.md` before any AirBoard operation.
- **Declaration present, anything else failing → declared but broken.** Report exactly which checks failed and what is missing; stop and ask the user how to proceed. Do **not** carry on in local mode — a declared repo drifting into local-file writes is exactly the dual-write confusion this protocol exists to prevent.
- **No declaration → local mode.** Say so. Offer onboarding (Mode 2) if the user wants AirBoard for this repo.

## Mode 2 — Onboard this repo onto AirBoard

Collect the inputs before writing anything. Two sources:

**Ask the AirBoard service owner** (whoever operates the instance — may be the same person as the user):

- Base URL of the AirBoard instance
- An API token for this repo's work, and the environment-variable name it should live under
- The project key for this repo — existing, or ask them to create one
- Anything about permissions or retention the team should know

**Ask the repo owner** (the user, or their team lead):

- Confirmation that AirBoard becomes the system of record for ADR / PRD / SPEC / PLAN / ISSUE on this repo
- Project key and token variable name, if not already fixed by the service owner
- What happens to existing local knowledge files (`docs/adr/`, `docs/specs/`, `docs/plan/`): frozen in place as a historical archive (the default — cheap and lossless) or migrated into AirBoard resources (manual; scope it explicitly — this skill does not migrate content on its own)
- Who else works on this repo — they all need to know that every new session reads the same `docs/airboard.md`

Then write, in this order:

1. **`docs/airboard.md`** — the shared machine config (template below).
2. **Root `AGENTS.md`** — add the declaration section (template below). The heading must contain "AirBoard" — the skills' probe matches on that.
3. **Repo-root `.env`** — append `<TOKEN_VAR>=<token>`. Verify `.env` is gitignored; never commit the token.
4. **Verify end to end** — `GET <base>/api/agents/instructions` responds; the project key resolves. Report the results.
5. **Commit** `docs/airboard.md` and `AGENTS.md` (never `.env`), following the repo's commit style.

### `docs/airboard.md` template

```md
# AirBoard config

This file is the machine-readable config every session on this repo reads before
any AirBoard operation. Do not hardcode these values anywhere else.

- **Base URL:** <base URL of the AirBoard instance>
- **Project key:** <PROJECT_KEY>
- **Token environment variable:** <TOKEN_VAR> — value lives in the repo-root `.env` (gitignored), sent as `Authorization: Bearer $<TOKEN_VAR>`

Fetch `GET <base>/api/agents/instructions` fresh before operating — it is the
machine contract. Resource GETs are slim by default; pass `?fields=full` to
read a body.
```

### `AGENTS.md` declaration template

```md
### AirBoard — knowledge tracking (system of record)

This repo runs **AirBoard mode**: ADRs, PRDs, SPECs, PLANs, and ISSUEs live in
AirBoard, never as new repo files. `docs/adr/`, `docs/specs/`, and `docs/plan/`
are frozen historical archives — do not add to them. What stays local:
`CONTEXT.md` (glossary) and the `.scratch/` workspace (gitignored).

- **Config:** read base URL, project key, and token environment variable from `docs/airboard.md` — every session on this repo reads that same config.
- **Contract:** fetch `GET /api/agents/instructions` fresh before operating.
- **IDs:** assigned by the server on `POST` — never number or pre-allocate IDs locally.
- **SPECs are living documents:** revise via `PATCH`; each revision is the change record.
- **Mode probe:** workflow skills detect this mode by this AGENTS.md section plus `docs/airboard.md`.
```

## Mode 3 — Onboard a person or agent to an AirBoard-mode repo

Walk them through, in this order:

1. Read `docs/airboard.md` → the three elements; read the token from the repo-root `.env`.
2. `GET <base>/api/agents/instructions` fresh, once per session, before any operation.
3. Reading rules: resource `GET`s are slim — `?fields=full` for bodies; search via `GET <base>/api/search?q=<terms>&kind=<KIND>&project_key=<key>`.
4. What lives where: ADR / PRD / SPEC / PLAN / ISSUE on AirBoard; `CONTEXT.md` (glossary) and `.scratch/` workspace local.
5. Never assign IDs yourself — `POST` and use what the server returns. (Parallel work lines numbering ADRs locally once produced duplicate IDs; server-side allocation is the fix.)
6. On failure (service unreachable, token missing, config unparseable): stop and ask — never silently fall back to local files.

## Guardrails

- Never write real base URLs, project keys, token variable names, or token values into skills, commits, or any repo doc other than `docs/airboard.md` — and token values go nowhere committed at all.
- Never remove or weaken the `AGENTS.md` declaration without the repo owner's explicit say-so — other sessions' mode detection depends on it.
- Never migrate historical docs as a side effect; migration is a separate, explicitly scoped task.
