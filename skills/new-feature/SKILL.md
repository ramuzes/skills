---
name: new-feature
description: The canonical develop→ship entry point. Decides whether a task is Bounded (well-scoped — short design, approval, implement) or Architectural (new flow, architecture, or interfaces — explore, approaches, domain-modeling, write-specs, then writing-plans). Use at the start of any non-trivial feature, refactor, or fix.
---

# New feature

The entry point for any non-trivial work in this workspace. This skill decides how heavy the process needs to be and walks the right path — so a one-screen fix doesn't get a spec, and a new subsystem doesn't get built without one.

<HARD-GATE>
Do NOT write code, scaffold, or take any implementation action until you have presented a design and the user has approved it. Presenting a design and starting to implement in the same breath is skipping the gate — this applies to BOTH paths below. When you reach a gate, STOP and wait for an explicit yes.
</HARD-GATE>

## Step 0 — Explore project context

Always first, for either path. Keep your context lean: read the small curated docs yourself, delegate the bulk code reading to explore subagents.

**Read directly:** `CONTEXT.md` and the ADRs in the area you'll touch. You design inside the model that already exists — you need the actual glossary wording, not a summary of it.

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

Pick **Architectural** if ANY are true:

- It adds new flow — a new feature or subsystem, not just extending an existing one
- It changes architecture: module boundaries, responsibilities, or data flow
- It updates protocols or interfaces — public APIs, schemas, event/message contracts
- It will need new domain language or a hard-to-reverse decision (→ `domain-modeling`)

Otherwise it's **Bounded**: a well-scoped change that fits the existing structure — a fix, refactor, or small extension where the seams are already known.

> When in doubt, default to Bounded. Escalate to Architectural the moment the user's answers reveal new flow or an interface change. You can switch paths mid-conversation; you cannot skip the gate.

## Path A — Bounded (well-scoped)

1. **Ask clarifying questions** — one at a time, only the ones that matter. Don't interview for the sake of it.
2. **Present a short design in chat** — approach, files touched, how it'll be tested. A few sentences each, scaled to complexity. For UI, a text description is enough (e.g. "a tab bar with two tabs: Positions and Orders") — no mockups, no visual companion.
3. **GATE — get approval.** STOP and wait for an explicit yes. Do not start implementing in the same message.
4. **Implement** — proceed with the normal development workflow. No plan document, no spec file.
   - Use `implement` for a single focused change; `subagent-driven-development` only if it splits into multiple tasks.
   - `test-driven-development` applies throughout.
   - Finish with `verification-before-completion`, then code-review.

## Path B — Architectural (new flow / architecture / interfaces)

1. **Ask clarifying questions** — one at a time. Goal: nail down purpose, constraints, and success criteria so every decision is made now, not during implementation.
2. **Propose 2–3 approaches** — with trade-offs and your recommendation. Lead with the recommended one and why. YAGNI ruthlessly.
   - **GATE — get the user to pick an approach** (or confirm your recommendation) before writing anything.
3. **Domain modeling** — invoke the `domain-modeling` skill to capture the decisions this work introduces:
   - Add or sharpen terms in `CONTEXT.md` as they crystallise.
   - Record hard-to-reverse, surprising, real-trade-off decisions as ADRs under `docs/adr/`.
   - Architectural work almost always moves the domain model — if nothing here changed, reconsider whether this is really Architectural.
4. **Write the spec** — invoke `write-specs`, passing the gathered requirements. `write-specs` writes the design doc to `docs/specs/YYYY-MM-DD-<feature>-design.md`, self-reviews it, and asks the user to review.
5. **GATE — user reviews the written spec** (run by `write-specs`). Wait for approval before planning. If the user requests changes, `write-specs` revises and re-reviews.
6. **Transition to implementation** — invoke the `writing-plans` skill to turn the spec into an implementation plan. writing-plans is the only skill you invoke after the spec; it then hands off to `subagent-driven-development` (TDD inside).

**The terminal state of Path B is invoking writing-plans.** Do not jump straight to coding.

## System of record

| Doc | Location | Producer | Lifecycle |
|---|---|---|---|
| Glossary | `CONTEXT.md` (root) | `domain-modeling` | persistent, curated |
| Decisions / guidelines | `docs/adr/` | `domain-modeling` | persistent, curated |
| **SPEC** — why+what (requirement) + how (design) | `docs/specs/YYYY-MM-DD-<feature>-design.md` | `write-specs` (called by `new-feature` Path B · `chat-to-spec`) | persistent |
| **PLAN** — implementation plan | `docs/plan/YYYY-MM-DD-<feature>.md` | `writing-plans` | persistent (committed) |
| sdd workspace (ledger, briefs, review packages) | `.scratch/sdd/<plan>/` | `subagent-driven-development` | scratch — gitignored, deleted on merge |

**No PRD doc** — requirements live inside each SPEC. **No issue tracker yet** — `to-tickets` is deferred; specs and plans are files, not issues.

**On merge:** delete the `.scratch/sdd/` workspace (git history is the record now); promote any non-obvious decision to a short ADR; update `CONTEXT.md` if new terms crystallised. The SPEC and PLAN stay.

## Session rules

- **Start every design/implementation session on a new branch.** All work for the feature stays on that one branch.
- **Worktree is opt-in** — only when you explicitly ask for one. Location: `.worktrees/` (gitignored). The default is a plain branch.
- **Code-review after every task**, whether or not you're using `subagent-driven-development`.
- **Final review checks spec compliance** (Path B) — was the SPEC/PLAN implemented correctly? — not code style.

## Related skills

- Bounded execute → `implement` (+ `test-driven-development`)
- Architectural: write the spec → `write-specs`; execute → `writing-plans` → `subagent-driven-development` (+ `test-driven-development`)
- Debug → `systematic-debugging`
- Explore intent / read the model → `grill-with-docs` (reads CONTEXT/ADRs)
- Standalone spec from a loose topic → `chat-to-spec` (user-invoked; collects requirements, then calls `write-specs`)

If a step has no sibling skill above, do the obvious thing — don't invent ceremony.
