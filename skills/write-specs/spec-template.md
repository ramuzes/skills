# Spec template

Write the SPEC to `docs/specs/YYYY-MM-DD-<feature>-design.md` using this structure. `<feature>` is a short kebab-case slug. Use the project's domain glossary (`CONTEXT.md`) vocabulary throughout, and respect any ADRs in the area you're touching.

This document holds both the **requirement** (the why and the what — what other teams call a PRD) and the **design** (the how). Requirements live here; we do not keep a separate PRD doc.

---

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

Do NOT include specific file paths or code snippets — they go stale fast.

### Testing decisions
- What makes a good test here (test external behaviour, not implementation details)
- Which modules will be tested
- Prior art for the tests (similar tests already in the codebase)

## Further notes
Anything else relevant.
