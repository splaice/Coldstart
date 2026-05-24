# _AGENTS.md

A skeleton for the cross-vendor `AGENTS.md` an AI agent
should encounter when it lands in this repo. Each section
names what belongs there and gives the agent populating it
a short hint list.

`AGENTS.md` is a **map**, not an encyclopedia. Its job is
to orient a fresh agent (Claude Code, OpenAI Codex CLI,
Gemini Code Assist, Cursor, etc.) in seconds: what is this
project, where do the rules live, which doc do I open for
the task in front of me, and where do vendor-specific
quirks belong. The rules themselves live in topic-rooted
docs; `AGENTS.md` points at them.

## How to use this template

This template is populated by an agent reading the repo as
it exists. It operates in one of two modes; pick first.

**Mode A — Existing codebase.** A non-trivial codebase is
already present (source dirs, build config, tests, real
top-level docs). Derive `AGENTS.md` from what is actually
there: read the repo root, the obvious topic docs, the
build/test config, and assemble the map from facts.

**Mode B — Cold start.** The repo contains little more
than the `_*.md` skeletons (`_BRIEF.md`, `_SPEC.md`,
`_ARCHITECTURE.md`, this file) and maybe a `README.md`.
Seed `AGENTS.md` to *bootstrap* the project: name the
skeletons as the current source of truth, list the topic
docs that should exist later as TBD rows, and mark the
file with `Mode: cold-start` in §0 so a future re-run
knows to regenerate from real content.

General rules in both modes:

- Keep it a **map**. If you find yourself explaining a
  rule, the rule belongs in a topic doc; `AGENTS.md` gets
  the pointer.
- Aim for **under ~100 lines** of body content. The
  Paranor reference enforces this with a size guard; you
  do not need a guard, but you do need the discipline.
- Name files by their actual on-disk path. Do not link
  them — agent harnesses resolve paths directly and links
  go stale faster than paths.
- Vendor-specific behavioral hints belong in the vendor's
  own file (`CLAUDE.md`, `GEMINI.md`, etc.), not here.
  This file is what every vendor agrees on.
- Omit a section the project does not need yet, but say
  so explicitly (`Not applicable: ...`) rather than
  deleting it silently — a future re-run needs to know
  the absence was deliberate.

---

## 0. Meta

A tiny header so a fresh agent knows what it is reading.

*Hints for the agent:*
- **Project name** is the canonical short name humans use.
- **Mode** is one of:
    - `existing` — derived from a real codebase. Re-run
      when the map drifts from `ls`.
    - `cold-start` — seeded before code exists. Re-run
      after the first real implementation lands; do not
      leave a cold-start AGENTS.md in place once topic
      docs exist for real.
- **Last reviewed** is a date. AGENTS.md is revisited,
  not released; a stale date tells the next agent to
  verify before trusting.

- **Project name:**
- **Mode:**
- **Last reviewed:**

---

## 1. What this project is

Two to four sentences. Concrete enough that an agent can
decide whether a task even belongs here.

*Hints for the agent:*
- **Existing mode:** derive from `README.md`, the SPEC, or
  the actual entry-point code. Name the runtime shape
  ("single Go binary serving a React UI", "Python CLI
  + library", "Rust workspace of N crates"), the storage
  story if relevant, and any single load-bearing external
  dependency.
- **Cold-start mode:** derive from `_BRIEF.md` §1 if
  present. If `_BRIEF.md` is empty too, write one honest
  sentence — "Greenfield repo; no implementation yet. See
  `_BRIEF.md` for intended shape." — and move on.
- Do NOT list features. Features belong in `README.md`.
  This section is about shape.

---

## 2. Repository map

A table: every top-level file or directory an agent should
know about, and one short phrase saying what it is.

*Hints for the agent:*
- One row per top-level entry that carries authority
  (SPECs, workflow docs, build config, generated-asset
  source-of-truth). Skip noise (`.gitignore`,
  `node_modules/`, lockfiles unless load-bearing).
- For each topic doc, name it as **authoritative for**
  one specific concern ("SPEC.md — service contract;
  authoritative for HTTP API, schema, auth model").
  Vague rows ("docs about stuff") are worse than no row.
- For **generated** files, mark them generated and name
  the generator. Agents otherwise edit them by mistake.
- If the project uses a task runner (`justfile`,
  `Makefile`, `package.json` scripts, `mise.toml`), list
  it and call it out as **the source of truth for "how
  to do X"**. This single row prevents an enormous
  amount of agent flailing.
- For directories with their own README/SPEC (`plans/`,
  `decisions/`, `migrations/`), one row pointing at the
  directory is enough — do not enumerate contents here.
- **Cold-start mode:** list the `_*.md` skeletons as the
  current authoritative docs, and add one row per topic
  doc that *will* exist (`SPEC.md`, `ARCHITECTURE.md`,
  `WORKFLOW.md`, etc.) marked `TBD — see _BRIEF.md`.

| File | What it is |
|------|------------|
| `path/to/thing` | one phrase; name what it is *authoritative for*. |
| `path/to/thing` | one phrase; name what it is *authoritative for*. |
| *(extend as needed; prune anything an agent does not need to know)* |

---

## 3. When to read what

A small set of `task → doc` pointers. The most useful
section in the file; populate carefully.

*Hints for the agent:*
- One bullet per recurring task class. Lead with the
  triggering action, end with the doc to open.
- Cover at minimum: shipping a change, touching schema /
  data model, touching auth or secrets, touching the
  build / CI gates, modifying the SPEC, understanding
  *why* the project is built a certain way.
- If a task spans two docs, list both in order
  ("`MIGRATIONS.md` + `SPEC.md` §8"). Order matters: the
  first one named is the entry point.
- **Cold-start mode:** point at the skeletons instead
  ("Before deriving SPEC → `_BRIEF.md`"). It is fine for
  this section to be short until real topic docs exist.
- Do NOT restate the content of the doc here. If the
  pointer is not enough, the doc title is wrong, or the
  topic is split across too many docs.

- Before shipping anything → *(doc)*.
- Touching schema / data model → *(doc)*.
- Touching auth / secrets / credentials → *(doc)*.
- Reviewing a CI failure → *(doc)*.
- Modifying the SPEC → *(doc + protocol)*.
- Understanding *why* a decision was made → *(doc /
  decisions directory)*.

---

## 4. Cross-tool notes

Where vendor-specific entry points live and what belongs
in each.

*Hints for the agent:*
- The cross-vendor convention is: **`AGENTS.md` is the
  shared map; each vendor's own file is a pointer plus
  vendor-specific behavioral hints.**
- Concretely, ensure these files exist alongside
  `AGENTS.md` (create them if missing):
    - `CLAUDE.md` — a single line: `@AGENTS.md`. (Claude
      Code's `@` directive inlines the referenced file.)
      Anything Claude-specific (tool-use preferences,
      slash-command notes) goes *below* the `@AGENTS.md`
      line in `CLAUDE.md`, not in `AGENTS.md`.
    - `GEMINI.md` — a short pointer paragraph (Gemini's
      auto-loader does not support `@`-includes); name
      `AGENTS.md` as the map, optionally list the top
      two or three topic docs.
    - Add `.cursor/rules/`, `.windsurf/`, `AGENT.md`
      (singular, Amp), etc. **only** if the project
      actually targets those tools. Do not preemptively
      create vendor files for tools nobody uses.
- The decision rule for "where does this rule go" is:
    - Applies across vendors → topic doc (linked from §2).
    - Vendor-specific → that vendor's file.
    - Never `AGENTS.md` itself — it is the map.

---

## 5. When this file is wrong

A short footer that gives the next agent permission to
trust the code over the map.

*Hints for the agent:*
- State the precedence explicitly: when `AGENTS.md`
  disagrees with the SPEC, the build config, the test
  suite, or the actual code, the code wins.
- Tell the agent what to do about it: fix the map as
  part of the PR they are already opening. Do not file
  a separate ticket for a one-line map fix.
- If the project has per-topic-doc "When this file is
  wrong" footers (the Paranor pattern), note that the
  same rule applies to each.

> When `AGENTS.md` and a topic doc contradict, the topic
> doc wins. When a topic doc and the code contradict, the
> code wins. Fix the map as part of your current PR.

---

## Maintenance notes (for future updates)

When revisiting this file:

1. Re-run `ls` over the repo root. Every row in §2
   should still map to a real path; every real top-level
   doc worth knowing about should have a row.
2. If `Mode: cold-start` is still set but real topic
   docs now exist, **switch to existing mode and
   regenerate from scratch**. Do not patch a cold-start
   map into an existing-codebase map.
3. Keep §3 short. Five to eight pointers is plenty; if
   the list grows past ten, the project probably needs
   to consolidate docs, not extend the map.
4. Resist adding sections. Five plus a meta is already
   a lot for a map. New content almost always belongs in
   a topic doc.
5. If a vendor file (`CLAUDE.md`, `GEMINI.md`, etc.) has
   drifted from this map, fix the vendor file — never
   the other direction. `AGENTS.md` is the source of
   truth; vendor files are pointers.
