# _WORKFLOW.md

A skeleton for the `WORKFLOW.md` an agent should produce
when it lands in this repo. Each section names what
belongs there and gives the agent populating it a short
hint list.

`WORKFLOW.md` is the **procedural** counterpart to the
mechanical merge gate. It describes how a feature lands —
worktree to teardown — in steps an agent can follow
without surprises. The mechanical enforcement (CI, lints,
guards) lives in the build system; this doc explains the
choreography around it.

A `WORKFLOW.md` is only as useful as the tooling it
depends on. Most of the sections below name a command or a
directory the workflow uses. If that command or directory
does not exist yet, the agent populating this file must
also stub it (see §S "Supporting items").

## How to use this template

This template is populated by an agent reading the repo
as it exists. It operates in one of two modes; pick first.

**Mode A — Existing codebase.** A non-trivial codebase is
present, with a real build/test system and at least one
prior commit on a feature branch. Derive `WORKFLOW.md`
from what is actually there: read the task-runner config
(`justfile`, `Makefile`, `package.json` scripts, etc.),
the CI/merge-gate definition, the branch / worktree
conventions visible in git, and any existing
`PLANS/` / `SPEC.md` / `TESTING.md`.

**Mode B — Cold start.** The repo contains little more
than the `_*.md` skeletons and maybe a `README.md`. There
is no task runner, no CI recipe, no `PLANS/` directory.
Populate `WORKFLOW.md` with **defensible defaults** (named
explicitly so a human can override), and write the
matching scaffolding into §S so the workflow becomes
followable instead of aspirational. Mark §0 `Mode:
cold-start` so a future re-run knows to regenerate from
real content once the project has shape.

General rules in both modes:

- The numbered sequence in §2 is the contract. Keep step
  ordering stable across rewrites — other docs reference
  steps by number (e.g., "§2.4 merge gate").
- Every step that names a command MUST point at a
  command that exists (existing mode) or appears in §S as
  a stub-to-create (cold-start mode). A workflow step
  whose command does not exist is worse than no step.
- Keep prose terse and imperative. A workflow is read
  under deadline pressure; long paragraphs lose readers.
- Anti-patterns belong in §4 "Don't do", not scattered
  through the sequence as inline warnings.
- Omit a section the project does not need yet, but say
  so explicitly (`Not applicable: ...`) rather than
  deleting it silently.

---

## 0. Meta

A tiny header so a fresh agent knows what it is reading.

*Hints for the agent:*
- **Project name** is the canonical short name humans
  use. Use it in the intro paragraph too.
- **Mode** is one of:
    - `existing` — derived from a real codebase. Re-run
      when tooling changes (new merge-gate steps, new
      branch convention, host migration).
    - `cold-start` — seeded before real tooling exists.
      Re-run once the project has a working task runner
      and a first feature has landed; do not leave a
      cold-start `WORKFLOW.md` in place forever.
- **Last reviewed** is a date. `WORKFLOW.md` is
  revisited, not released; a stale date tells the next
  agent to verify before trusting.

- **Project name:**
- **Mode:**
- **Last reviewed:**

---

## 1. What this workflow assumes

A short paragraph plus a bullet list of the tooling /
host / conventions every step below relies on. Lets a
reader sanity-check fit before reading the sequence.

*Hints for the agent:*
- Name, at minimum: the task runner (`just` / `make` /
  `npm` / `pnpm` / `cargo` / etc.), the VCS host (GitHub
  via `gh` / GitLab via `glab` / Gerrit / etc.), the
  branching model (worktrees / branches in main checkout),
  and the merge gate's name (`just ci` / `make check` /
  `npm test` / etc.).
- Each item gets one line. If the project does not use a
  task runner at all, say so — the sequence below will
  use bare commands instead.
- **Cold-start mode:** pick a defensible default and tag
  it `(default — confirm)`. Justify the default in one
  short clause if it is not obvious.
- This section is the place to mention SMTP, container
  registries, or any external service the workflow
  itself depends on (not the product — the workflow).

- **Task runner:**
- **VCS host / PR tool:**
- **Branching model:**
- **Merge gate command:**
- **Other workflow-time dependencies:**

---

## 2. The sequence

The numbered steps a feature passes through. Mirror this
order; downstream docs reference steps by number.

*Hints for the agent:*
- Use the same eight-step shape unless a step is
  genuinely irrelevant (mark `Not applicable: ...`).
- Each step is at most a few sentences plus, where
  useful, a short code block. If a step needs more
  explanation, the explanation belongs in the doc the
  step links to (e.g., `TESTING.md`), not inline.
- Code blocks MUST be fenced and closed. A dangling
  ` ```bash ` swallows the next section header — a
  surprisingly common bug in hand-edited workflow docs.
- Reference other docs by file name only (`TESTING.md`,
  `SECURITY.md`). Do not link them; paths beat links for
  agent resolution.

### 2.1 Create a worktree (or branch)

Trigger: starting any non-trivial change.

*Hints for the agent:*
- If the project uses worktrees, give the exact command
  and the resulting path. State the directory convention
  (e.g., `../.<project>-harness/worktrees/<branch>`) and
  why it lives where it does — most agents will follow
  the convention only if they see the reason.
- If the project does not use worktrees, replace this
  step with the local branching convention.
- State the branch-name rules (prefixes like `feat/` /
  `fix/` / `chore/` / `docs/`, no direct pushes to
  `main`).
- **Cold-start mode:** default to `git worktree` if the
  project plans to support multiple parallel agents;
  default to plain `git checkout -b` otherwise. Tag the
  decision `(default — confirm)`.

### 2.2 Plan if non-trivial

Trigger: work spans more than a single small change.

*Hints for the agent:*
- Point at the `PLANS/` directory (or whatever the
  project calls it) and a per-plan filename convention
  (e.g., `PLANS/<NAME>_PLAN.md`).
- Specify the plan format briefly: a top-of-file `- [ ]`
  checklist so progress is grep-able.
- State the lifecycle rule explicitly: plans are
  short-lived, deleted in the same PR that ships the
  feature. This rule prevents the `PLANS/` directory
  from rotting into a graveyard.
- **Cold-start mode:** if `PLANS/` does not exist, add a
  §S row to create it with a stub `PLANS/README.md`.

### 2.3 Implement

Trigger: plan in place (or step skipped as trivial).

*Hints for the agent:*
- This step is a short prefer-list, not an essay. Three
  to six bullets is plenty.
- Common bullets: test-first, edit-before-create,
  integration-over-unit (with the project's test helper
  if any), comment discipline (explain *why*, not
  *what*).
- Reference language-specific test patterns only if the
  project is single-language. For polyglot repos, point
  at `TESTING.md` instead.

### 2.4 Run the merge gate locally

Trigger: implementation believed complete.

*Hints for the agent:*
- Name the exact command and state explicitly whether
  there is a remote CI runner. If the project's
  philosophy is "merge gate is local", say so — agents
  otherwise assume a GitHub Actions safety net.
- State who is responsible: "whoever pushes is
  responsible for green CI before requesting review."
- If the gate has a documented chain (lint → typecheck
  → tests → security), point at `TESTING.md` for the
  breakdown rather than restating it here.

### 2.5 Commit and push

Trigger: merge gate is green.

*Hints for the agent:*
- Commit-message style: conventional-commit prefixes
  (`feat:` / `fix:` / `chore:` / `docs:` / `test:` /
  `refactor:`) unless the project uses something else.
- One logical change per commit; push with
  `--set-upstream` on first push.
- If the project requires `Signed-off-by:` trailers
  (DCO), say so explicitly with the trailer's exact
  syntax. If it does not, do not mention sign-off.

### 2.6 Open the PR / MR

Trigger: branch is pushed and CI is green.

*Hints for the agent:*
- Give the exact command (`gh pr create ...` or
  equivalent) inside a fenced code block.
- State what the PR body MUST contain at minimum: link
  to the `PLANS/*_PLAN.md` from §2.2 if one exists, a
  one-paragraph context, and a test plan.
- Do not capture the PR number into a shell variable
  unless a later step uses it; dead captures confuse
  agents that read literally.

### 2.7 Wait for human merge

Trigger: PR is open, CI is green.

*Hints for the agent:*
- State the rule in one sentence: agents do not merge
  their own PRs.
- Give the agent a concrete *trigger* for pinging the
  human (e.g., "once CI is green and the PR is open, tag
  the human for review"). Without a trigger, agents
  either ping too early or wait forever.

### 2.8 Tear down

Trigger: PR is merged.

*Hints for the agent:*
- Give the exact teardown command (`just
  worktree-cleanup` or the equivalent: branch delete +
  worktree remove + dev-data wipe).
- State what gets wiped (e.g., a `<project>-data/`
  directory left behind by `just dev`) so an agent who
  cares about the state knows what they are losing.
- Restate the "plan deleted in the same PR that ships
  the feature" rule from §2.2 so it is impossible to
  miss.

---

## 3. SPEC.md change protocol

**Conditional section.** Include only if the project has
or plans to have a `SPEC.md` (or equivalent contract
document). If not, write `Not applicable: project has no
SPEC.md.` and move on.

*Hints for the agent:*
- The point of this protocol is to prevent agents from
  silently editing the project's contract as a
  side-effect of other work. It has two parts: a
  procedural rule (operator must authorize) and a
  mechanical guard (a CI check that fails without an
  explicit marker).
- Pick a marker syntax (e.g., `[spec-change-authorized]`
  in a commit message; or a magic trailer like
  `Spec-Authorized-By: ...`). Document the exact string;
  the guard will key on it.
- The matching guard belongs in §S as a `spec-guard`
  recipe (or equivalent) to be created if missing.
- Require the operator's authorization to be quoted
  **verbatim** in the PR body so reviewers can verify it
  without trusting the agent's summary.

---

## 4. Don't do

A flat bullet list of anti-patterns. Read this when
deciding "should I do X?".

*Hints for the agent:*
- Each bullet is one short imperative ("Don't push to
  `main` directly."). Long explanations belong in the
  section that defines the rule.
- Cover at minimum: direct pushes to the default branch,
  self-merging PRs, touching another agent's worktree,
  bypassing the merge gate, modifying the SPEC outside
  the protocol (if §3 is present).
- Project-specific bullets matter most here. If `just
  dev` has a known port collision risk, if `paranor-data/`
  can be wiped by accident, if a particular recipe has
  no idempotency guarantee — say so.

- Don't push to `<default-branch>` directly.
- Don't merge your own PRs.
- *(extend with project-specific anti-patterns)*

---

## 5. When this file is wrong

A short footer that gives the next agent permission to
trust the code over the doc.

*Hints for the agent:*
- State precedence explicitly: when `WORKFLOW.md`
  disagrees with the merge-gate recipe, the task-runner
  config, or the actual scripts, the code wins.
- Tell the agent what to do about it: fix the doc as
  part of their current PR, not in a follow-up.

> If a rule here contradicts what `<merge-gate>`,
> `<task-runner-config>`, or `<orchestration-scripts>`
> actually enforce, the code wins. Fix this doc as part
> of your current PR.

---

## S. Supporting items (scaffolding to create)

A checklist of files, directories, recipes, and external
configuration the workflow above depends on. Existing
mode: list only what is **missing** and needs creating.
Cold-start mode: this section will be long — that is
expected, and is the point of cold-start mode.

*Hints for the agent:*
- For each item, give: the path / name, what it is,
  which §2 step depends on it, and the minimum content
  needed for the workflow to be followable.
- Group by category: task-runner recipes, directories,
  reference docs, host setup. A flat list of 20 items
  is harder to triage than four short groups.
- Do not silently produce these artifacts. List them as
  a checklist so the operator can review what will be
  created before the agent starts writing files.
- A workflow step that depends on a missing item should
  link forward to its §S entry, so the reader can see
  the gap before they hit it.

### S.1 Task-runner recipes

The verbs the workflow uses. Stub these in the task
runner if absent.

- [ ] **`worktree-new <branch>`** — creates a worktree
  at the project's worktree path; required by §2.1.
- [ ] **`worktree-cleanup <branch>`** — removes worktree,
  deletes branch, wipes dev data; required by §2.8.
- [ ] **`ci`** — the merge gate; required by §2.4. Stub
  with whatever lint+test commands the project has today;
  point at `TESTING.md` for the full chain.
- [ ] **`spec-guard`** — only if §3 is in scope.
  Required by §3 and §2.4 (gate's first step).
- [ ] *(extend with any other verb the sequence names)*

### S.2 Directories

- [ ] **`PLANS/`** with a one-paragraph `PLANS/README.md`
  describing the per-plan filename convention and the
  "deleted in the shipping PR" rule; required by §2.2.
- [ ] **Worktree storage path** — usually a sibling
  hidden directory (e.g., `../.<project>-harness/
  worktrees/`); required by §2.1. Document the path in
  the worktree recipe and in §2.1 prose.
- [ ] *(extend with any other directory the sequence
  names)*

### S.3 Reference docs

- [ ] **`TESTING.md`** — the merge gate's full chain,
  test conventions; referenced by §2.3 / §2.4.
- [ ] **`SECURITY.md`** — referenced by §4 if the
  project has security-sensitive paths.
- [ ] **`SPEC.md`** — only if §3 is in scope.
- [ ] *(extend with any other doc the sequence names)*

### S.4 Host / external setup

- [ ] **VCS host CLI authenticated** (`gh auth status` /
  `glab auth status` / etc.) — required by §2.6.
- [ ] **Default-branch protection** configured to reject
  direct pushes — backstops §4.
- [ ] *(extend with any other host-side setup)*

---

## Maintenance notes (for future updates)

When revisiting this file:

1. Re-run the task-runner help (`just --list` /
   `make help` / `npm run`) and verify every verb §2
   names still exists. Renamed or removed verbs are the
   most common drift.
2. If `Mode: cold-start` is still set but real recipes
   and a `PLANS/` directory now exist, **switch to
   existing mode and regenerate from scratch**. Do not
   patch a cold-start workflow into an existing one.
3. Step numbering in §2 is a stable contract. If you
   need to add a step, prefer a `.5` insertion
   (`§2.5.5`) over renumbering, so other docs'
   cross-references survive.
4. The "Don't do" list grows over time as incidents
   teach new lessons. Prune entries only when the
   underlying foot-gun has been removed mechanically,
   not when "we should know better by now."
5. Supporting items (§S) should shrink as the project
   matures. A repo whose §S still has open checkboxes a
   year in is signaling that nobody is actually
   following the workflow.
