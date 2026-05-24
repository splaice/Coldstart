# _ARCHITECTURE.md

A skeleton for an ARCHITECTURE.md derived by reading an
existing codebase. Each section names what belongs there
and gives the agent populating it a short hint list.

The purpose of ARCHITECTURE.md, per matklad
(<https://matklad.github.io/2021/02/06/ARCHITECTURE.md.html>),
is to bridge the gap between occasional contributors and
core developers. The biggest difference between the two is
knowledge of the project's **physical** structure: writing
a patch when unfamiliar takes ~2x longer, but figuring out
*where* to make the change takes ~10x longer. ARCHITECTURE.md
collapses that 10x for the next contributor.

It is not a SPEC. It is not a design doc. It is a
contributor-facing map of an existing repo.

## How to use this template

- This template is populated by an agent reading the
  current codebase, not by transforming `_BRIEF.md` or
  `_SPEC.md`. Brief and SPEC describe intent; ARCHITECTURE
  describes the code that exists.
- Aim for a document a recurring contributor can re-read
  in one sitting. If it grows past ~3 screens of prose, it
  is doing too much — push detail into module-level docs.
- **Name** important files, modules, and types in prose.
  **Do not** hyperlink them — links go stale faster than
  names do. Trust the reader to use symbol search.
- Only describe things unlikely to change often. Volatile
  details (current ticket lists, sprint goals, half-built
  refactors) do not belong here.
- Do not try to keep this synchronized with every commit.
  Revisit it a few times a year, or when a major rewrite
  invalidates an invariant.
- The codemap is **a map of a country, not an atlas of
  maps of its states**. Each module gets one short blurb,
  not a sub-architecture.
- Omit a section the project genuinely does not need, but
  say so explicitly ("Not applicable: ...") rather than
  deleting it silently.

---

## 0. Meta

A tiny header that orients a fresh reader in three lines.

*Hints for the agent:*
- **Last reviewed** is a date, not a version. ARCHITECTURE
  is revisited, not released. A reader seeing a stale date
  should know to verify before trusting.
- **Audience** is "a developer about to make their first
  non-trivial change," not end-users and not operators.
  If you find yourself writing for either, you are in the
  wrong document.
- **Scope** names the repo or sub-tree this file covers.
  Mono-repos may want one ARCHITECTURE.md per top-level
  package rather than a single mega-doc.

- **Last reviewed:**
- **Audience:**
- **Scope:**

---

## 1. Bird's-eye overview

Three to six sentences. What problem does this codebase
solve, and what is the one-paragraph shape of its solution?

*Hints for the agent:*
- Lead with the problem, not the solution. A reader who
  does not understand *why* the code exists cannot judge
  whether a change is in scope.
- Name the dominant abstraction once ("event-sourced
  ledger", "pull-based scheduler", "single-binary CLI with
  a daemon mode") — the codemap will reference it.
- If the system has an obvious analogue ("like make, but
  for X"), use it. Analogies cheaply transfer mental model.
- Do NOT enumerate features here. Features belong in
  README; ARCHITECTURE is about shape.

---

## 2. Codemap

Coarse-grained modules and how they relate. Five to fifteen
entries. Each gets a name (the actual directory or module
name) and a one-to-three sentence blurb.

The codemap exists to answer two questions, in order:
1. *Where is the thing that does X?*
2. *What does the thing I am already looking at do?*

*Hints for the agent:*
- One entry per coarse module, in the order a new reader
  would benefit from meeting them — usually
  outside-in (entry point → core → leaves) or
  request-flow order.
- Each blurb is responsibility, not implementation. "Owns
  the on-disk representation of X" beats "uses Y crate to
  serialize X."
- Name a module by its directory path (e.g. `src/parser/`)
  or its canonical Rust/Python/TS module identifier
  (`crate::parser`), not by a marketing-style label.
- Do not link the module. The reader will use their
  editor's symbol/file search; that survives renames
  better than a stale link.
- If two modules look like they overlap, the codemap is
  where you call out the line between them ("`X` owns
  the wire format; `Y` owns the in-memory representation").
- Resist nesting. A flat list of 10 modules with crisp
  blurbs beats a tree of 25 with vague ones.

- **`module/path/`** — one-to-three sentence blurb.
- **`module/path/`** — one-to-three sentence blurb.
- **`module/path/`** — one-to-three sentence blurb.
- *(extend as needed; stop well before fifteen)*

---

## 3. Architectural invariants

Properties that hold across the whole codebase, often
expressed as the *absence* of something.

*Hints for the agent:*
- The most useful invariants are negative: "module A never
  depends on module B," "no code outside `io/` performs
  disk I/O," "the parser never allocates," "no panics in
  the request path." Negative invariants prune entire
  classes of bug from a reviewer's search.
- Each invariant should be one sentence. If it needs a
  paragraph, it is probably a design doc, not an invariant.
- State the invariant, then (optionally) where in the code
  it is enforced or checked. "Enforced by lint rule X" /
  "Enforced by the type system" / "Conventional; not
  mechanically checked" are all valid annotations.
- Do not list invariants that are merely *true today* but
  not load-bearing. The bar is: "if this were violated,
  something important would silently break."

- **Invariant:** one sentence. *Enforcement:* one phrase.
- **Invariant:** one sentence. *Enforcement:* one phrase.
- *(extend as needed)*

---

## 4. Layer boundaries

Where the major layers and subsystems meet, and what
crosses each boundary.

*Hints for the agent:*
- A boundary worth naming is one a contributor will
  accidentally violate. "UI never imports from `db/`
  directly; all access goes through `services/`" is
  exactly the kind of guidance that prevents a class
  of bad PRs.
- For each boundary, name the two sides and the
  permitted direction of dependency. Bidirectional
  dependencies are a smell; if one exists, call it out
  rather than hiding it.
- If the project uses a plugin / extension surface,
  describe the contract at the boundary: what the host
  guarantees the plugin, and what the plugin must
  guarantee the host.
- This is often the shortest section. That is fine.

---

## 5. Cross-cutting concerns

Things that touch many modules and therefore do not live
cleanly inside any single codemap entry.

*Hints for the agent:*
- Typical cross-cutting concerns: error handling
  strategy, logging/tracing conventions, configuration
  loading, feature-flag plumbing, concurrency / async
  runtime, testing harness, build system quirks.
- For each concern, one short paragraph: what the
  convention is, and which module owns it if any.
- If the project has a non-obvious convention that bites
  newcomers (e.g. "all timestamps are UTC seconds, never
  millis; serialized as integers"), this is the place.
- Do not duplicate content from §2 or §3. If a concern
  is already covered by an invariant, link to the
  invariant by name.

---

## 6. What is *not* here

A short list of decisions a reader might expect to find
in this document but will not.

*Hints for the agent:*
- Point at where those decisions actually live: a SPEC,
  a design doc directory, inline module docs, a wiki,
  an RFC repo. One line each.
- This section also serves as a non-goal list for the
  ARCHITECTURE itself — it tells future maintainers what
  *not* to drag in here when they update it.
- If the project has no SPEC and no design docs, say so;
  do not invent destinations.

- **Per-module internals:** see module-level docs / doc
  comments in each module.
- **Normative behavior:** see `SPEC.md` (if one exists).
- **Product/feature decisions:** see `_BRIEF.md` (if one
  exists) or the changelog.
- **Operational runbooks:** *(point to runbook location
  or note "none")*.

---

## Maintenance notes (for future updates)

When revisiting this file:

1. Re-read it cold. Anything you no longer recognize is
   either stale or worth keeping because it caught you.
2. Update §0 `Last reviewed` only if you actually re-read
   the whole document, not just patched one section.
3. Prefer **removing** a stale invariant or boundary to
   "softening" it. An invariant that is sometimes true
   is worse than no invariant at all.
4. If the codemap has drifted by more than a couple of
   entries, the doc is overdue for a rewrite, not a
   patch — start from a fresh reading of the repo.
5. Resist adding sections. Six is already a lot. New
   content usually belongs in a module-level doc.
