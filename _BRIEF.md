# _BRIEF.md

A skeleton for planning briefs that derive cleanly into a SPEC.md.
Each section names what belongs there and gives the agent
helping a human fill it out a short hint list.

The brief supports two modes — pick first:

- **Cold-start.** Greenfield project; no implementation exists
  yet. The brief captures intent before code: what to build,
  what not to build, what's load-bearing. The user is the
  source of truth.
- **Warm-start.** A codebase already exists. The brief reverse-
  engineers the design by reading code, tests, configs,
  READMEs, and existing decisions — *not* to transcribe the
  code, but to surface **intent vs. accident**. What was
  deliberately decided, vs. what just happened? The human
  resolves the difference before the brief closes.

## How to use this template

### Mode-specific guidance

**Cold-start mode.** Work top-to-bottom; earlier decisions
constrain later ones. The user answers; ask before guessing.
Use `?? ... ??` to mark anything the user has not yet
decided. No `BRIEF_BLOCKERS.md` is created.

**Warm-start mode.** Read the codebase first (entry points,
public surfaces, tests, config files, READMEs, any ADRs)
before writing anything. Then fill the brief from
observation. Every section's hints below assume a cold-start
collaborator — translate them: where a hint says "push the
user to write...", read it as "look for the equivalent fact
in the code, and if it isn't there, invent a reasonable one
and mark it `!!`". Two new disciplines apply:

- Mark **ambiguities** with `?? ... ??` — places where the
  code permits two readings and you cannot tell which was
  intended.
- Mark **invented or recommended facts** with `!! ... !!` —
  places where you chose a value the code does not pin
  down (a name for a behavior, a guessed limit, a category,
  an inferred convention). Both kinds of marker stay in the
  brief body until the human resolves them.
- Write a companion `BRIEF_BLOCKERS.md` (format below)
  enumerating every `??` and `!!` site as a checklist.
  Status (§0) stays `OPEN` until that file is empty or
  deleted.

### General rules (both modes)

- Mark normative rules as one of `MUST`, `SHOULD`, `MAY`, or
  `IMPL-DEFINED` (variation point the SPEC names but doesn't
  fix).
- Resolve every `?? ... ??` and `!! ... !!` marker — or move
  it to the Decision Log — before the brief closes.
- Keep the brief stable. When a decision changes, update both
  the affected section AND the Decision Log — don't leave the
  brief and the log saying different things.
- The brief should describe entities, behaviors, and decisions.
  State-machine transition tables and pseudocode belong in
  SPEC.md, not here.
- When the brief gets long enough that you can't scan it,
  consider whether sections are duplicating content; cross-
  references between sections beat re-statements.

### `BRIEF_BLOCKERS.md` (warm-start only)

A working checklist the agent writes alongside the brief in
warm-start mode. It lists every `??` and `!!` site so the
human can resolve them in one sweep. Deleted once empty.

Format:

```markdown
# BRIEF_BLOCKERS.md

Companion to `BRIEF.md` (spec version: <X>, mode: warm-start).
Resolve every item before closing the brief.

## Ambiguities to resolve (`?? ... ??`)

- [ ] BL-1 · §<loc> · `?? <marker text> ??`
      What:      <what is unclear from the code>
      Options:   <two or more possible readings>
      Needs:     <human decision>
      Resolved:  <YYYY-MM-DD — what was decided>

## Invented / recommended facts to confirm (`!! ... !!`)

- [ ] BL-N · §<loc> · `!! <marker text> !!`
      What:      <fact the agent chose>
      Reason:    <why this default seemed reasonable>
      Needs:     <human confirm or override>
      Resolved:  <YYYY-MM-DD — confirmed, or replaced with X>
```

Numbering is stable: never renumber on resolution. When the
file holds zero unchecked items, delete it and flip §0 Status
to `CLOSED`.

---

## 0. Meta

Tiny header that orients a fresh reader in three lines.

*Hints for the agent:*
- **Spec version** is a single alphabetical character
  starting at `A` for the first cut. Subsequent rewrites
  step to `B`, `C`, etc. Avoids the "v0 / v1" anachronism
  creeping into prose later. The version does NOT appear
  anywhere in the body — only here.
- **Mode** is one of:
    - `cold-start` — greenfield; brief precedes code.
    - `warm-start` — codebase exists; brief is derived
      from code and reviewed for intent. A companion
      `BRIEF_BLOCKERS.md` exists while drafting.
- **Status** is one of exactly two values:
    - `OPEN` — the brief is still being drafted; sections
      may contain `?? ... ??` (and, in warm-start mode,
      `!! ... !!`) markers, the Decision Log may have
      unresolved items, and `BRIEF_BLOCKERS.md` (warm-
      start) may hold unchecked items.
    - `CLOSED` — all decisions are settled, no open
      markers remain in the body, `BRIEF_BLOCKERS.md`
      has been deleted (warm-start) or never existed
      (cold-start), and the brief is ready to derive
      a SPEC.md from.
- "Out-of-band assumptions" is for things readers should
  already know (RFC 2119, the host platform, the target
  language ecosystem). Don't restate them in the body.

- **Spec version:**
- **Mode:**
- **Status:**
- **Out-of-band assumptions:**

---

## 1. Problem Statement

Three to six sentences. No marketing language.

*Hints for the agent:*
- One-sentence "what does this do?" in present tense.
- Identify the trigger: external signal, schedule, user action?
- Operational problems should be concrete enough that you
  could point to a pre-existing manual workflow each replaces.
- **Hard boundary statements** ("X is a Y; X is NOT a Z") are
  the most load-bearing content here — they prevent scope
  creep at every later section. Push the user to write at
  least one explicit non-boundary.

- **What does this system do?**
- **Who/what triggers it?**
- **What operational problems does it solve?**
- **Hard boundary statements:**

### 1.a Scenarios

Two to four vignettes that anchor the product. Every later
section should be re-checkable against these.

*Hints for the agent:*
- Vignettes should be concrete: a named role, a specific
  action, a tangible result.
- Cover the happy path AND at least one safety/abuse path —
  the brief needs to make both feel buildable.
- If you find yourself writing a scenario that contradicts
  §1 boundaries, the scenario is probably out of scope.

1. **[Scenario name]**:
2. **[Scenario name]**:
3. **[Scenario name]**:

The product is the smallest thing that makes [happy paths]
feel trivial without making [safety path] impossible.

---

## 2. Goals & Non-Goals

Two flat lists. Both REQUIRED.

*Hints for the agent:*
- Goals are **outcomes**, not features. "Polls on a fixed
  cadence with bounded concurrency" beats "has a poller."
- Non-goals are the highest-leverage section in the brief.
  Each non-goal kills an entire class of future feature
  requests. Push the user to write at least three.
- If a non-goal is "no X surface for customers," distinguish
  customer-facing from internal-operator-facing — they're
  not the same and conflating them causes confusion later.

- **Goals:**
- **Non-Goals:**

### 2.a Architectural Anchor

If one architectural decision is load-bearing for everything
downstream (trust model, identity model, pricing model,
extensibility model), give it a named subsection here.
Optional but often clarifying.

*Hints for the agent:*
- This is where to anchor a decision that shows up everywhere
  later. Naming it once with a clear label means other
  sections can reference §2.a instead of restating.
- If the goals and security posture seem to be in tension
  (low friction vs. real anti-abuse, fast iteration vs.
  durable guarantees), the anchor here often resolves that
  tension with an explicit framing.
- If your project doesn't need an anchor yet, omit. Don't
  invent one.

---

## 3. System Overview

### 3.a Main Components

Five to fifteen named subsystems, each with a 1–3 bullet
responsibility blurb.

*Hints for the agent:*
- Pick stable nouns; every later section references these.
- Avoid over-decomposition: "File-level X / Agent-level X /
  Operator-level X" is usually one subsystem viewed three
  ways. Collapse.
- Cross-reference §4 entities — components act on entities;
  they should name them.

### 3.b External Dependencies

What the system requires from outside itself. Mark each
REQUIRED or OPTIONAL.

*Hints for the agent:*
- Include distribution channels (npm, GitHub Releases,
  Homebrew, etc.) as a dependency — how does the software
  get to the user?
- Identity providers, secret stores, observability backends,
  payment processors all count.
- Note free-tier characteristics where they affect the
  architecture (e.g. "egress is free on this storage backend"
  is load-bearing for cost modeling).

---

## 4. Core Domain Model

The most under-specified section in most plans. Vague entity
names here cause every later section to be vague.

*Hints for the agent:*
- Aim for 6–12 entities. Each gets a name, a one-line
  purpose, and (where applicable) its identifier shape.
- Distinguish internal IDs from public/external slugs. They
  have different constraints (sortability vs. opacity,
  guessability vs. usability).
- Identify the **identifier rules** at the end of this
  section: what's an ULID, what's a hash, what's a random
  base62 string, when do they differ.
- Capture entity visibility scopes early — who can see what,
  whose data is which Operator's — those rules belong as
  domain-level statements, not buried in §9.
- "Block" / "Suspension" / "Disable" / "Removed" /
  "Hard-deleted" are commonly conflated; if your project has
  multiple kinds of block, name and distinguish them
  explicitly with their HTTP / surface semantics.

---

## 5. Repository / External Contracts

Files, schemas, CLI surfaces, MCP tool surfaces — anything
the system reads, writes, or exposes that is part of its
public contract.

*Hints for the agent:*
- For each contract: name, path/location, format, schema,
  validation, error classes, reload behavior.
- If there's a CLI, the subcommand surface IS a contract.
  Enumerate subcommands grouped by family.
- If there's an MCP server, the tool surface IS a contract.
  Note which CLI commands have MCP-tool equivalents and
  which don't (e.g. auth subcommands typically don't, since
  agents can't drive them autonomously).
- If JSON output is part of the surface, commit the envelope
  shape, field naming conventions, stable error codes, and
  must-have fields per surface. SPEC fills in the rest.
- Distinguish public contracts (breaking them breaks users)
  from server-internal config (server-side knobs, not
  versioned).

---

## 6. Configuration

Client config, env-var overrides, server config, with
precedence and reload semantics.

*Hints for the agent:*
- Resolution order matters: env > flag > file > default
  (or whatever you pick). Pin it.
- For each setting: default, reload behavior (immediate /
  next-tick / restart-required).
- If your project has tier or quota envelopes, list specific
  numbers here, even if rough — "100 MB / day" beats "the
  default daily storage cap" for SPEC derivation.
- Free-tier numbers are usually best informed by cost
  analysis of the dominant cost driver (storage × time,
  per-request CPU, LLM call cost, etc.).

---

## 7. Behavioral Specifications

The bulk of the brief. One sub-section per major behavior.

*Hints for the agent:*
- For each behavior: trigger / inputs, algorithm (numbered
  steps, normative MUST / SHOULD / MAY), concurrency model,
  failure handling.
- If a single process is best modeled as a pipeline (cheap
  checks then expensive ones, or pre-something vs.
  post-something), name the pipeline structure explicitly so
  future extension is obvious.
- For long-running async operations (device flows, polling),
  describe the polling shape and the terminal states.
- Reference §4 entities by name; don't reintroduce vocabulary.

Typical behaviors to cover:
- The hot path (request-and-return).
- Long-running operations (auth, batch).
- Admin operations (suspension, deletion, takedown).
- Reporting / abuse handling.
- Cross-system reconciliation if applicable.

---

## 8. Subsystem Protocols / Integration Contracts

For each external thing the system talks to: required
operations, wire format, normalization rules, error handling
contract, timeouts.

*Hints for the agent:*
- Cross-reference §3.b dependencies; this section adds
  protocol-level detail, not new dependencies.
- Be explicit about what the system writes vs. only reads
  on each integration. "X is read-only from our side" is a
  load-bearing boundary worth restating.
- For identity providers specifically, note any
  provider-specific quirks: relay email handling, tenant
  claims, version handling.

Trust boundary: state explicitly which parties the service
trusts and which it doesn't.

---

## 9. Cross-Cutting Concerns

### 9.a Logging & Observability

*Hints for the agent:*
- Log format (structured JSON? key-value?).
- Required vs. recommended trace fields per major event.
- Metric cardinality discipline — high-cardinality data
  (per-entity counts, per-IP rates) belongs in an OLAP /
  analytics surface, not a Prometheus-style metrics system.
- If analytics have a freshness requirement, name it
  explicitly (real-time / minutes / overnight). Determines
  the backend.

### 9.b Failure Model

*Hints for the agent:*
- Failure classes (transient / configuration / policy /
  fatal). Each class gets a recovery behavior.
- Restart recovery: what survives, what doesn't, what's
  reconstructed and how.
- Operator intervention points: what humans need to do, when.

### 9.c Security & Safety

*Hints for the agent:*
- Trust posture: explicit one-line statement.
- Content / input policy: deny-list or allow-list? Detection
  method (extension / mime / magic-byte)? What happens on
  disagreement?
- Secret handling: where credentials live, what's never
  logged.
- Abuse channel: how external parties report problems.
- If the product surfaces public URLs, state the
  unguessability / auth-gating posture explicitly.

---

## 10. Conformance & Test Matrix

What makes an implementation correct.

*Hints for the agent:*
- One row per testable behavior. Map to §7 behaviors
  one-to-one where possible.
- Include negative tests (what should NOT happen) as well
  as positive ones.
- Include cross-Operator / cross-tenant isolation tests if
  the system has multi-tenancy semantics.
- Include "no information leak" tests where applicable —
  e.g., querying a resource you don't own should return the
  same response as querying a non-existent resource.

---

## 11. Decision Log

Settled decisions (do not re-litigate). Add open operational
items here as a checklist subsection when they need
attention; remove them once resolved.

Each settled entry, one line:
`- **Decision name (date):** one-line summary. See §X for detail.`

*Hints for the agent:*
- The Decision Log is an index, not a re-statement. Detail
  lives in the section the decision affects; the log just
  points there.
- Settled decisions get a date stamp. Useful when reading
  the log in 18 months.
- If a decision was reversed mid-planning, note the reversal
  explicitly so future readers can trace the trajectory
  without confusion.
- Operational / SPEC-level items that are deferred can sit
  in a separate checklist subsection until they need
  attention; remove them entirely once the planning session
  ends if they won't be tracked here.

---

## Translation notes (for the SPEC.md author)

When converting this brief into SPEC.md:

1. Add normative-language preamble (RFC 2119 plus a
   definition of `IMPL-DEFINED`).
2. Promote every `?? ... ??` to a resolved decision or an
   explicit `IMPL-DEFINED` marker.
3. Number sections so cross-references stay stable.
4. Name every entity once and reuse the name forever.
5. Every config field gets a default — even if the default
   is `IMPL-DEFINED`.
6. Every error gets a stable name string.
7. State machines and reference algorithms live in SPEC.md,
   not the brief. If the brief includes them, treat as
   informative rather than normative.
8. Cross-check the brief's §10 Conformance against the SPEC's
   actual behaviors — gaps in either direction signal
   under- or over-specification.
