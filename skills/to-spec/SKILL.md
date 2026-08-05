---
name: to-spec
description: Turn the current conversation into a SPEC and write it to docs/spec/<feature>.md — no interview, just synthesis of what you've already discussed.
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a **SPEC** — one document that holds both the *requirement* (the why and the what — what other teams call a PRD) and the *design* (the how). Do NOT interview the user — just synthesize what you already know. Requirements live here; we do not keep a separate PRD doc.

This repo has **no issue tracker** — the SPEC is a committed file, not an issue.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary (`CONTEXT.md`) vocabulary throughout, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better — the ideal number is one.

Check with the user that these seams match their expectations.

3. Write the SPEC to **`docs/spec/<feature>.md`** using the template below. `<feature>` is a short kebab-case slug. This file is the durable design record — once approved, `writing-plans` turns it into an implementation plan.

<spec-template>

# <Feature name>

## Requirement (the why & what)

### Problem statement
The problem the user is facing, from the user's perspective.

### Goal
The outcome this SPEC delivers — what is measurably different when it's done.

### User stories
A long, numbered list. Each: *As a \<actor>, I want \<feature>, so that \<benefit>.*
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending.

### Out of scope
Things this SPEC deliberately does not cover.

## Design (the how)

### Solution
The approach, from the user's perspective.

### Implementation decisions
- Modules to build/modify and their interfaces
- Technical clarifications
- Architectural decisions
- Schema changes / API contracts

Do NOT include specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose (state machine, reducer, schema, type shape), inline it within the relevant decision and note it came from a prototype.

### Testing decisions
- What makes a good test here (test external behaviour, not implementation details)
- Which modules will be tested
- Prior art for the tests (similar tests already in the codebase)

## Further notes
Anything else relevant.

</spec-template>
