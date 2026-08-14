---
name: chat-to-spec
description: Turn a free-form topic into a spec. User-invoked standalone on-ramp — collect requirements and info through clarifying questions, then invoke write-specs to produce the reviewed spec at docs/specs/YYYY-MM-DD-<feature>-design.md. Use even when no other skill is running.
disable-model-invocation: true
---

# Chat-to-spec

A standalone, user-invoked way to turn a loose topic into a spec. This skill **collects** requirements through dialogue, then hands them to `write-specs` (the pure writer) to produce the spec. Use it when you have a topic in mind but aren't running the full `new-feature` loop.

## Process

1. **Explore the repo** to understand the current state. Use the project's domain glossary (`CONTEXT.md`) vocabulary throughout, and respect any ADRs in the area you're touching.

2. **Collect requirements through clarifying questions** — one at a time. Pin down the problem, the goal, scope, and success criteria. If the conversation already contains enough, synthesize instead of re-asking. Sketch the test seams you expect to use and check they match the user's expectations.

3. **Invoke `write-specs`**, passing the gathered requirements (and the relevant chat history) as context. `write-specs` writes the spec to `docs/specs/YYYY-MM-DD-<feature>-design.md`, self-reviews it, and asks the user to review.

## After the spec

`write-specs` stops at a reviewed spec. To implement, run `new-feature` (it routes based on scope) or invoke `writing-plans` directly to turn the spec into a plan.
