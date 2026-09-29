---
name: new-feature
description: The canonical develop→ship entry point. Decides whether a task is Bounded (well-scoped — short design, approval, implement) or Architectural (new flow, architecture, or interfaces — explore, approaches, domain-modeling, write-specs, then writing-plans). Use at the start of any non-trivial feature, refactor, or fix. Dual-mode knowledge store — local files (docs/adr/, docs/specs/, docs/plan/) or AirBoard resources (ADR/PRD/SPEC/PLAN/ISSUE) when the repo declares AirBoard mode.
---

# New feature

The entry point for any non-trivial work in this workspace. This skill decides how heavy the process needs to be and walks the right path — so a one-screen fix doesn't get a spec, and a new subsystem doesn't get built without one.

<HARD-GATE>
Do NOT write code, scaffold, or take any implementation action until you have presented a design and the user has approved it. Presenting a design and starting to implement in the same breath is skipping the gate — this applies to BOTH paths below. When you reach a gate, STOP and wait for an explicit yes.
</HARD-GATE>

## Step 0 — Explore project context

Always first, for either path. Keep your context lean: read the small curated docs yourself, delegate the bulk code reading to explore subagents.

> **AirBoard mode** is active for a repository iff its root `AGENTS.md`
> contains a section declaring AirBoard as the system of record (heading
> contains "AirBoard") **and** `docs/airboard.md` exists; otherwise the repo
> runs in **local mode**. In AirBoard mode, resolve base URL / project key /
> token env var from `docs/airboard.md` (token value in the repo-root `.env`,
> gitignored). Before the first AirBoard operation, fetch
> `GET <base>/api/agents/instructions?profile=discovery` and follow it as the
> machine contract — it is the **sole source of protocol semantics**; any API
> details cached in this skill text are non-authoritative. On unreachable
> service / missing token / unparseable config: stop and ask — never silently
> fall back to local mode.

(To check or set up a repo's AirBoard config, the user can invoke the `airboard-onboarding` skill.)

**Read directly:** `CONTEXT.md` and the ADRs in the area you'll touch. You design inside the model that already exists — you need the actual glossary wording, not a summary of it. **AirBoard mode:** search the service for the area's ADRs, per the contract. **Local mode:** read the local files in `docs/adr/`.

**Formulate questions before dispatching.** List the specific things this feature needs to know about the codebase — the answers your design and clarifying questions depend on. Typically:

- Which modules own the behavior being changed, and who calls them?
- What's the nearest prior art — an existing pattern for work like this?
- Where are the test seams today, and which are extendable?
- What changed recently in this area (commits, in-flight work)?
- Is there a half-built or abandoned version of this already in the tree?

"Explore the project" is not a prompt — it produces a vague report. Questions first, dispatch second.

**Dispatch explore subagents with those questions.** They're read-only and independent of each other — run them in parallel, one question-cluster per agent. Ask each for conclusions with `file:line` pointers, not file dumps. Scale it: Bounded work usually needs one agent (or none, if you already know the answers); Architectural work justifies one per subsystem involved.

The findings feed Step 1 and the clarifying questions — they tell you what to ask.

## Step 1 — Decide the path

**First, judge the request's clarity.** If the user arrived with an unsure or unclear statement — a vague idea, a fuzzy goal, undecided scope, or decisions visibly still open — do not guess the missing intent into a design. Invoke the `grilling` skill and grill the user, round by round, until the plan and its decisions are settled; when the answers start minting new domain terms or hard-to-reverse decisions, bring `domain-modeling` in alongside. Those answers decide the path. A request already clear enough to route needs no grilling — the lighter one-at-a-time questions in the paths below cover it.

Pick **Architectural** if ANY are true:

- It adds new flow — a new feature or subsystem, not just extending an existing one
- It changes architecture: module boundaries, responsibilities, or data flow
- It updates protocols or interfaces — public APIs, schemas, event/message contracts
- It will need new domain language or a hard-to-reverse decision (→ `domain-modeling`)

Otherwise it's **Bounded**: a well-scoped change that fits the existing structure — a fix, refactor, or small extension where the seams are already known.

> When in doubt, default to Bounded. Escalate to Architectural the moment the user's answers reveal new flow or an interface change. You can switch paths mid-conversation; you cannot skip the gate.

## Path A — Bounded (well-scoped)

1. **Ask clarifying questions** — one at a time, only the ones that matter. Don't interview for the sake of it. If grilling already ran in Step 1, its answers stand in for these — ask only what is still open.
2. **Present a short design in chat** — approach, files touched, how it'll be tested. A few sentences each, scaled to complexity. For UI, a text description is enough (e.g. "a tab bar with two tabs: Positions and Orders") — no mockups, no visual companion.
3. **GATE — get approval.** STOP and wait for an explicit yes. Do not start implementing in the same message.
4. **Implement** — proceed with the normal development workflow. No plan document, no spec file.
   - Use `implement` for a single focused change; `subagent-driven-development` only if it splits into multiple tasks.
   - `test-driven-development` applies throughout.
   - Finish with `verification-before-completion`, then code-review.

## Path B — Architectural (new flow / architecture / interfaces)

1. **Ask clarifying questions** — one at a time. Goal: nail down purpose, constraints, and success criteria so every decision is made now, not during implementation. If grilling already ran in Step 1, its answers stand in for these — ask only what is still open.
2. **Propose 2–3 approaches** — with trade-offs and your recommendation. Lead with the recommended one and why. YAGNI ruthlessly.
   - **GATE — get the user to pick an approach** (or confirm your recommendation) before writing anything.
3. **Domain modeling** — invoke the `domain-modeling` skill to capture the decisions this work introduces:
   - Add or sharpen terms in `CONTEXT.md` as they crystallise.
   - Record hard-to-reverse, surprising, real-trade-off decisions as ADRs — per `domain-modeling`'s current mode: AirBoard ADR resources in AirBoard mode, files under `docs/adr/` in local mode.
   - Architectural work almost always moves the domain model — if nothing here changed, reconsider whether this is really Architectural.
4. **Write the spec** — invoke `write-specs`, passing the gathered requirements. `write-specs` writes the design doc to `docs/specs/YYYY-MM-DD-<feature>-design.md` (local mode) or as an AirBoard SPEC resource (AirBoard mode), self-reviews it, and asks the user to review.
5. **GATE — user reviews the written spec** (run by `write-specs`). Wait for approval before planning. If the user requests changes, `write-specs` revises and re-reviews.
6. **Transition to implementation** — invoke the `writing-plans` skill to turn the spec into an implementation plan. writing-plans is the only skill you invoke after the spec; it then hands off to `subagent-driven-development` (TDD inside).

**The terminal state of Path B is invoking writing-plans.** Do not jump straight to coding.

## System of record

| Doc | Location | Producer | Lifecycle |
|---|---|---|---|
| Glossary | `CONTEXT.md` (root) — both modes | `domain-modeling` | persistent, curated |
| Decisions / guidelines | **AirBoard mode:** ADR resources · **local mode:** `docs/adr/` | `domain-modeling` | persistent, curated |
| **PRD** — external requirements | **AirBoard mode only:** PRD resources (entered by stakeholders via the AirBoard web UI; SPECs cite them as `Ref: PRD-N`) | stakeholders | persistent |
| **SPEC** — why+what (requirement) + how (design) | **AirBoard mode:** SPEC resource (living — revised via `PATCH`) · **local mode:** `docs/specs/YYYY-MM-DD-<feature>-design.md` | `write-specs` (called by `new-feature` Path B · `chat-to-spec`) | persistent |
| **PLAN** — implementation plan | **AirBoard mode:** PLAN resource · **local mode:** `docs/plan/YYYY-MM-DD-<feature>.md` | `writing-plans` | persistent (committed; AirBoard mode: `done` once merged) |
| sdd workspace (ledger, briefs, review packages) | `.scratch/sdd/<plan>/` — both modes | `subagent-driven-development` | scratch — gitignored, deleted on merge |

**Local mode has no PRD doc** — requirements live inside each SPEC; **in AirBoard mode a PRD is an AirBoard resource** — the external requirements entry point for people who never touch the repo, consumed by SPECs. **Local mode has no issue tracker yet** — `to-tickets` is deferred, and specs and plans are files, not issues; **in AirBoard mode, AirBoard ISSUE resources are the tracker** — `to-tickets` can publish to them.

**On merge:** delete the `.scratch/sdd/` workspace (git history is the record now); promote any non-obvious decision to a short ADR (AirBoard ADR resource or `docs/adr/` file, per mode); update `CONTEXT.md` if new terms crystallised. **Local mode:** the SPEC and PLAN files stay. **AirBoard mode:** `PATCH` the PLAN to `status: done`; the SPEC stays `active` as a living document — later changes go through SPEC revisions, not new specs.

## Session rules

- **Start every design/implementation session on a new branch.** All work for the feature stays on that one branch.
- **Worktree is opt-in** — only when you explicitly ask for one. Location: `.worktrees/` (gitignored). The default is a plain branch.
- **Code-review after every task**, whether or not you're using `subagent-driven-development`.
- **Final review checks spec compliance** (Path B) — was the SPEC/PLAN implemented correctly? — not code style.

## Related skills

- Unclear input → `grilling` (invoked by Step 1 when the request itself is fuzzy; pairs with `domain-modeling` when decisions crystallize)
- Bounded execute → `implement` (+ `test-driven-development`)
- Architectural: write the spec → `write-specs` (SPEC as an AirBoard resource or a `docs/specs/` file, per mode); execute → `writing-plans` → `subagent-driven-development` (+ `test-driven-development`)
- Debug → `systematic-debugging`
- Explore intent / read the model → `grill-with-docs` (user-invoked wrapper: grilling + domain-modeling, with docs)
- Standalone spec from a loose topic → `chat-to-spec` (user-invoked; collects requirements, then calls `write-specs`)
- AirBoard config check / repo onboarding → `airboard-onboarding` (user-invoked; sets up or diagnoses a repo's AirBoard mode)

If a step has no sibling skill above, do the obvious thing — don't invent ceremony.
