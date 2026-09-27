# ADR Format

Where an ADR lives depends on the repo's knowledge mode:

> **AirBoard mode** is active for a repository iff its root `AGENTS.md`
> contains a section declaring AirBoard as the system of record (heading
> contains "AirBoard") **and** `docs/airboard.md` exists; otherwise the repo
> runs in **local mode**. In AirBoard mode, resolve base URL / project key /
> token env var from `docs/airboard.md` (token value in the repo-root `.env`,
> gitignored). Before the first AirBoard operation, fetch
> `GET <base>/api/agents/instructions?profile=authoring` and follow it as the
> machine contract — it is the **sole source of protocol semantics**; any API
> details cached in this skill text are non-authoritative. On unreachable
> service / missing token / unparseable config: stop and ask — never silently
> fall back to local mode.

- **AirBoard mode:** an ADR is a service-side resource. `POST` it and **the server assigns the ID** (`ADR-N`). The title is passed separately on the resource; the body carries the decision.
- **Local mode:** ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc. Create the `docs/adr/` directory lazily — only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

That's it. An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why* — not in filling out sections. The body structure is identical in both modes; **AirBoard mode** passes the short title separately, so the body may drop the top-level heading and open with the 1–3 sentences.

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`) — useful when decisions are revisited. **AirBoard mode** uses the resource's `status` field instead: `proposed` → `draft`, `accepted` → `active`; `deprecated` / `superseded` → create the replacement ADR (`POST`), `PATCH` the old one to `status: cancelled`, and cross-link both ways (new body: `Supersedes: ADR-N`; old body: `Superseded by: ADR-M`).
- **Considered Options** — only when the rejected alternatives are worth remembering
- **Consequences** — only when non-obvious downstream effects need to be called out

## Numbering

**AirBoard mode:** none, ever. `POST` the resource and the server assigns `ADR-N` — never scan for a "next" number, never pre-allocate an ID for later use. Local scan-and-increment numbering collides the moment two parallel work lines do it (it has happened); server-side allocation is the fix.

**Local mode:** Scan `docs/adr/` for the highest existing number and increment by one.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will look at the code and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If a decision is easy to reverse, skip it — you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

### What qualifies

- **Architectural shape.** "We're using a monorepo." "The write model is event-sourced, the read model is projected into Postgres."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events, not synchronous HTTP."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library — just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** "Customer data is owned by the Customer context; other contexts reference it by ID only." The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** "We're using manual SQL instead of an ORM because X." Anything where a reasonable reader would assume the opposite. These stop the next engineer from "fixing" something that was deliberate.
- **Constraints not visible in the code.** "We can't use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the rejection is non-obvious.** If you considered GraphQL and picked REST for subtle reasons, record it — otherwise someone will suggest GraphQL again in six months.
