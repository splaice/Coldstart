# _SPEC.md

A skeleton for specifications derived from a completed
`_BRIEF.md`. Each section names what belongs there and
gives the agent deriving the SPEC a short hint list.

A SPEC.md is the normative, implementable form of a brief.
The brief decides; the SPEC pins every decision down to the
point where an implementor needs no further input.

## How to use this template

- This template is populated by an agent transforming a
  `CLOSED` `_BRIEF.md` into a SPEC. Do NOT derive from an
  `OPEN` brief — unresolved `?? ... ??` (and, for warm-
  start briefs, `!! ... !!`) markers must be settled, and
  any `BRIEF_BLOCKERS.md` companion file must be empty or
  deleted before derivation starts.
- **Warm-start derivation has an extra duty.** When the
  source brief is `Mode: warm-start`, cross-check every
  brief claim against the actual code as you derive each
  SPEC section. Any disagreement — the brief says one
  thing and the code does another — becomes a `CODE-DRIFT`
  item in §13, marked `BLOCKING`. The resolution decides
  which side is right (brief → amend the brief; code →
  file as a bug). Do not silently align the SPEC with the
  code; the drift is the finding.
- Work top-to-bottom. SPEC §N is derived primarily from
  brief §N; the numbering is kept aligned so cross-
  references survive the transform.
- The brief uses `MUST` / `SHOULD` / `MAY` / `IMPL-DEFINED`.
  The SPEC keeps the same keywords and adds the RFC 2119
  preamble in §0. Do not weaken or strengthen a keyword
  during derivation without logging it in §13.
- The SPEC is normative prose. Where the brief gestured at
  a behavior, the SPEC states the exact algorithm, the
  exact field, the exact default, the exact error name.
- Anything the brief left open, contradicted, got wrong,
  or simply never supplied becomes an entry in §13 (Open
  Items & Resolution Checklist) — never a silent guess.
- Name every entity once (in §4) and reuse that name
  verbatim everywhere. Never reintroduce vocabulary.
- State-machine transition tables and reference pseudocode
  live here (§7, §11); they MUST NOT appear in the brief.
- Omit a section or sub-section the project genuinely does
  not need, but say so explicitly ("Not applicable: ...")
  rather than deleting it silently.

---

## 0. Preamble & Normative Language

A short header that orients a fresh reader and fixes the
meaning of every normative keyword used below.

*Hints for the agent:*
- Carry **Spec version** and **Status** forward from brief
  §0 Meta. Status here is `DRAFT` while §13 holds any
  `BLOCKING` item, and `READY` once it holds none.
- State the RFC 2119 interpretation of `MUST` / `MUST NOT`
  / `SHOULD` / `SHOULD NOT` / `MAY`.
- Define `IMPL-DEFINED`: a variation point the SPEC names
  but deliberately does not fix; each implementation
  chooses, and MUST document, its own behavior.
- Restate the brief's out-of-band assumptions (host
  platform, language ecosystem, RFC 2119). Do not re-
  derive them in the body.
- Record which brief version this SPEC was derived from,
  so later drift between the two is traceable.

- **Spec version:**
- **Status:**
- **Derived from brief version:**
- **Out-of-band assumptions:**

---

## 1. Problem Statement

Three to six sentences of normative scope-setting prose.

*Hints for the agent:*
- Derive from brief §1. Keep it present-tense and free of
  marketing language.
- Carry every **hard boundary statement** ("X is a Y; X is
  NOT a Z") through verbatim — these are the load-bearing
  scope fences and SPEC readers rely on them.
- The trigger (external signal / schedule / user action)
  becomes a precise statement here and is expanded in §7.
- Do not introduce a behavior here that no §7 sub-section
  specifies. If the brief implied one, log it in §13.

### 1.a Scenarios

The brief's vignettes, carried forward as the re-checkable
anchor for every later section.

*Hints for the agent:*
- Keep the brief §1.a scenarios; tighten wording to match
  the §4 vocabulary.
- Each scenario MUST be traceable to one or more §7
  behaviors and one or more §10 conformance rows. A
  scenario that maps to nothing is a §13 item.

---

## 2. Goals and Non-Goals

Two flat lists. Both REQUIRED.

*Hints for the agent:*
- Derive from brief §2. Goals stay outcome-shaped.
- Non-goals are normative scope exclusions: nothing in §3–
  §12 may contradict a non-goal. Cross-check, and log any
  conflict in §13.
- Distinguish customer-facing from operator-facing
  surfaces if the brief did.

### 2.a Architectural Anchor

If brief §2.a named a load-bearing architectural decision
(trust model, identity model, pricing model, extensibility
model), restate it here as a normative framing that other
sections reference by number.

*Hints for the agent:*
- Omit if the brief had no anchor. Do not invent one.
- Every later section that depends on the anchor should
  reference §2.a rather than restating it.

---

## 3. System Overview

### 3.a Components

The named subsystems from brief §3.a, each with a 1–3
bullet normative responsibility statement.

*Hints for the agent:*
- Use the brief's nouns verbatim. Each component name is
  now a fixed term reused everywhere below.
- Each component should name the §4 entities it acts on
  and the §7 behaviors it owns.
- If two components differ only by viewpoint, collapse
  them — and note the collapse in §13 if it diverges from
  the brief.

### 3.b Abstraction Levels

OPTIONAL. If the system has clean layers (e.g. policy /
configuration / coordination / execution / integration /
observability), list them so later sections can place
themselves.

*Hints for the agent:*
- Omit if the architecture is flat. Do not manufacture
  layers.

### 3.c External Dependencies

What the system requires from outside itself. Each marked
REQUIRED or OPTIONAL.

*Hints for the agent:*
- Derive from brief §3.b. §8 adds protocol-level detail;
  this section only enumerates.
- Include distribution channels. A dependency the brief
  omitted but §7 / §8 clearly needs is a §13 item.

---

## 4. Core Domain Model

The vocabulary spine of the SPEC. Every other section
draws its nouns from here.

*Hints for the agent:*
- Derive from brief §4. This is usually the thinnest part
  of a brief and the highest-leverage part of the SPEC.
- Any field a §7 behavior reads or writes MUST appear on
  some entity here. If the brief never named it, add it
  and log a `MISSING-FACT` in §13.

### 4.a Entities

One sub-block per entity: name, one-line purpose, and an
explicit field list with types and (where applicable)
defaults.

*Hints for the agent:*
- Aim for 6–12 entities. Use the brief's names verbatim.
- Distinguish internal IDs from public/external slugs —
  they have different constraints.

### 4.b Identifiers & Normalization Rules

*Hints for the agent:*
- For each identifier: its shape (ULID / hash / random
  base62 / composed key) and exactly how it is derived.
- State every normalization rule explicitly — case
  folding, sanitizing untrusted strings into filesystem-
  safe or URL-safe forms, composing compound keys.
  Implementors get these wrong silently.
- If the domain has several kinds of "block" / "remove" /
  "disable" / "delete", name and distinguish each with its
  surface semantics.

---

## 5. Contracts

Files, schemas, CLI surfaces, API surfaces, tool surfaces
— anything the system reads, writes, or exposes as a
public contract.

*Hints for the agent:*
- Derive from brief §5. For each contract pin: name,
  path/location, format, full schema, validation rules,
  error classes, and reload behavior.
- For file contracts: discovery / path-resolution
  precedence, parse rules, and the exact returned shape.
- For CLI / MCP / API surfaces: enumerate every command or
  endpoint, its inputs, its output envelope, and its
  stable error codes.
- Every error gets a stable name string here — implementors
  and conformance tests both depend on it. An error the
  brief implied but did not name is a §13 `MISSING-FACT`.
- Separate public contracts (breaking them breaks users)
  from server-internal config (unversioned knobs).

---

## 6. Configuration Specification

Every setting, its type, default, source precedence, and
reload semantics.

*Hints for the agent:*
- Derive from brief §6.
- State the **resolution pipeline** as an ordered list:
  source/path selection → parse → defaults → variable and
  secret expansion → validation.
- Every config field gets a default — even if the default
  is `IMPL-DEFINED`. A field with no brief-supplied default
  is a §13 `MISSING-FACT` until resolved.
- For each field: type, default, reload behavior
  (immediate / next-cycle / restart-required), and the
  canonical env-var / flag name if any.
- Specify **dynamic reload semantics**: what is re-read,
  what an invalid reload does, what is re-validated.
- Specify **preflight validation**: the checks that gate
  the system from starting or from dispatching work.
- End with a flat field-summary cheat sheet
  (name · type · default · reload · source).

---

## 7. Behavioral Specifications

The bulk of the SPEC. One sub-section per major behavior.
This section turns the brief's descriptions into exact,
implementable algorithms.

*Hints for the agent:*
- Derive from brief §7. Every behavior the brief described
  in prose becomes a numbered algorithm here.
- Reference §4 entities and §3.a components by name only;
  never reintroduce vocabulary.
- For each behavior: trigger / inputs, numbered algorithm
  steps each carrying `MUST` / `SHOULD` / `MAY`, the
  concurrency model, and failure handling (cross-ref §9.b).
- A step whose outcome the brief left open is either
  `IMPL-DEFINED` (state it) or a §13 item (log it) — never
  a silent choice.

### 7.a Lifecycle State Machine

If any entity has a non-trivial lifecycle, give its states
and transition table here.

*Hints for the agent:*
- List the states with a one-line meaning each.
- Give a transition table: (state, trigger) → state, with
  the side effects of each transition.
- Enumerate the triggers separately (timers, external
  events, process exits, reconciliation passes).
- State idempotency and recovery rules: which mutations
  are serialized, what reconciliation corrects, what
  startup reconstructs.
- Omit only if no entity has a lifecycle.

### 7.b … (one sub-section per remaining behavior)

*Hints for the agent:*
- Typical behaviors: the hot path (request-and-return),
  long-running async operations (give the polling shape
  and the terminal states), admin operations, reporting /
  abuse handling, cross-system reconciliation.
- If a behavior is best modeled as a pipeline, name the
  pipeline stages explicitly so later extension is obvious.

---

## 8. Integration Contracts

For each external system the system talks to: required
operations, wire format, normalization rules, error-
handling contract, and timeouts.

*Hints for the agent:*
- Derive from brief §8. Cross-reference §3.c; add protocol
  detail, not new dependencies.
- For each integration: the exact set of operations the
  system invokes, the request/response shape, how
  responses are normalized into §4 entities, the error
  categories, and every timeout (connect / read / overall
  / stall).
- State explicitly what the system **writes** vs. only
  **reads** on each integration. A read-only boundary is
  load-bearing — restate it.
- State the **trust boundary**: which parties the system
  trusts and which it does not.

---

## 9. Cross-Cutting Concerns

### 9.a Logging & Observability

*Hints for the agent:*
- Derive from brief §9.a. Pin the log format, the required
  vs. recommended trace fields per major event, and the
  context fields that MUST appear on every line.
- Specify any runtime snapshot / monitoring / status
  surface and mark it OPTIONAL or REQUIRED. An OPTIONAL
  surface MUST NOT become a correctness requirement.
- Name any analytics freshness requirement (real-time /
  minutes / overnight) if the brief stated one.

### 9.b Failure Model & Recovery

*Hints for the agent:*
- Derive from brief §9.b. Enumerate failure classes
  (transient / configuration / policy / fatal) and give
  each a normative recovery behavior.
- Specify retry / backoff precisely: the formula, the cap,
  what schedules a retry, and what cancels one.
- Restart recovery: what survives a restart, what does
  not, and what is reconstructed and how.
- List the operator intervention points.

### 9.c Security & Operational Safety

*Hints for the agent:*
- Derive from brief §9.c. State the trust posture in one
  line.
- State input / content policy (allow-list vs. deny-list,
  the detection method, the behavior on disagreement).
- State filesystem / resource safety invariants as
  numbered `MUST`s.
- Secret handling: where secrets live, the indirection
  mechanism, what is never logged.
- Give deployment-hardening guidance as `SHOULD` / `MAY`,
  kept separate from the mandatory invariants.

---

## 10. Conformance & Test Matrix

What makes an implementation correct.

*Hints for the agent:*
- Derive from brief §10 and cross-check against the §7
  behaviors actually specified — a gap in either direction
  is a §13 item.
- One row per testable behavior: id, the behavior / §
  reference, the assertion, and the expected result.
- Include negative tests (what must NOT happen) and
  isolation / no-information-leak tests where the domain
  has multi-tenancy.
- If the SPEC has OPTIONAL extensions, group rows into
  conformance profiles (core vs. extension vs. real-
  integration).

---

## 11. Reference Algorithms

OPTIONAL but RECOMMENDED. Language-agnostic pseudocode for
the load-bearing algorithms in §7.

*Hints for the agent:*
- Include pseudocode only for algorithms whose §7 prose is
  genuinely ambiguous without it — typically startup, the
  main loop, reconciliation, dispatch, and retry handling.
- Pseudocode here is informative: it MUST agree with the
  normative prose in §7. If they diverge, §7 wins and the
  divergence is a §13 item.

---

## 12. Implementation Checklist (Definition of Done)

The checklist an implementor uses to confirm conformance.

*Hints for the agent:*
- Every item should be checkable against a §10 row or a
  specific section. An item with no backing section is a
  §13 item.

### 12.a REQUIRED for Conformance

One bullet per `MUST` cluster in the SPEC.

### 12.b RECOMMENDED Extensions

OPTIONAL features and `SHOULD`s.

### 12.c Operational Validation Before Production

Real-integration checks, host/OS verification, and any
surface-specific validation before shipping.

---

## Appendix A — Optional Extensions

OPTIONAL. One sub-section per feature that is out of scope
for core conformance but specified for implementations
that choose to ship it.

*Hints for the agent:*
- Move a behavior here (rather than §7) only if the brief
  or the §2 non-goals mark it as non-core.
- Each extension still gets entities, algorithms, and
  conformance rows — scoped to its own profile.
- Delete this appendix if there are no extensions.

---

## 13. Open Items & Resolution Checklist

The running record of everything the deriving agent could
not resolve from the brief alone. This section is the
SPEC's hand-back to the human: until §13.a holds zero
`BLOCKING` items, the SPEC Status (§0) stays `DRAFT`.

*Hints for the agent:*
- This section is **append-only during derivation**. The
  moment deriving a section forces you to confront a gap,
  add an item here — do not defer or batch.
- Log an item whenever you hit any of:
  - `CONTRADICTION` — two parts of the brief (or two SPEC
    sections derived from it) state things that cannot
    both be true.
  - `ERROR` — the brief states something factually wrong,
    impossible, or inconsistent with a constraint it
    itself states.
  - `AMBIGUITY` — a passage can be implemented two ways,
    the difference is observable, and an implementor would
    be blocked or forced to guess.
  - `MISSING-FACT` — fulfilling the SPEC requires a fact
    the brief never supplied: a default value, a limit, an
    error name, a timeout, an enum member, a precedence
    order, an identifier shape. Anything you would
    otherwise have to invent silently goes here.
  - `CODE-DRIFT` — (warm-start derivation only) the brief
    claims a behavior the code does not exhibit, or the
    code exhibits a behavior the brief does not capture.
    Always `BLOCKING`. Resolution decides which side is
    right: amend the brief if the code is correct, file an
    implementation bug if the brief is correct. Do not
    silently align the SPEC with the code — the drift
    itself is the finding.
- For every `MISSING-FACT`, do NOT leave the SPEC blank:
  put a best-effort `IMPL-DEFINED` or a clearly-marked
  provisional value inline in the relevant section AND log
  the item here — so the SPEC stays readable while the gap
  stays visible.
- Mark each item `BLOCKING` (an implementor cannot proceed
  correctly without a human decision) or `NON-BLOCKING` (a
  reasonable provisional value is in place; confirmation
  improves the SPEC but is not required to start work).
- When an item is resolved: check its box, fill in the
  resolution and date, move it to §13.b, apply the fix to
  the affected SPEC section, and — if the brief itself was
  wrong or incomplete — apply the fix back to the brief
  and its §11 Decision Log so the two never disagree.
- Do not delete resolved items. A resolved checklist is
  the audit trail of how the SPEC was hardened.

**Item format:**

```
- [ ] OI-N · <CATEGORY> · <BLOCKING|NON-BLOCKING> · §<loc>[ ↔ §<loc>]
      What:        <one-line description of the gap>
      Impact:      <what an implementor gets wrong without this>
      Provisional: <the value/behavior currently in the SPEC, or "none">
      Needs:       <human decision | new fact | brief amendment | clarification>
      Resolution:  <filled in on resolution — what was decided — YYYY-MM-DD>
```

`CATEGORY` is one of `CONTRADICTION`, `ERROR`, `AMBIGUITY`,
`MISSING-FACT`, `CODE-DRIFT` (warm-start only). `§<loc>`
points at the SPEC section the item affects; use `§A ↔ §B`
for a contradiction spanning two locations, and add
`(brief §N)` when the root cause is in the brief.

### 13.a Open Items

*(append items here during derivation; empty when the SPEC
is first created)*

If this list is empty, write "No open items." and set the
§0 Status to `READY`.

### 13.b Resolved Items

*(move items here once their box is checked; keep the
Resolution line filled in)*

### 13.c Readiness Gates

Before setting §0 Status to `READY`, confirm:

- [ ] §13.a holds no `BLOCKING` item.
- [ ] Every `?? ... ??` marker from the brief became a
      resolved decision or an explicit `IMPL-DEFINED`.
- [ ] Every config field (§6) has a default.
- [ ] Every error (§5, §8) has a stable name string.
- [ ] Every entity (§4) is named once and reused verbatim.
- [ ] Every §1.a scenario maps to a §7 behavior and a §10
      row.
- [ ] No §3–§12 statement contradicts a §2 non-goal.
- [ ] §10 and §7 cover each other with no gap in either
      direction.
