# AI Standards

> A Manual for Using LLM Agents on Real Projects Without Turning Git History Into a Diary of Model Hallucinations

**Document Version:** 1.5.0  
**Last Updated:** 2026-06-04  
**Audience:** Future me and anyone using AI on real repos(larpers) — this is a **human** playbook, not a system prompt.  
**Scope:** Cross-project standards for AI-assisted planning, implementation, documentation, and (rarely) git operations  

> [!WARNING]
> **Examples are illustrative, not prescriptive**
> Paths, filenames, and commands in this document (`codingprocess.md`, `phase.md`, `ref/`, `.public.txt`, `.status.txt`, `cargo test`, etc.) are **examples** from real workflows. Your repo may use different names or(prolly) omit files entirely. Follow the *rules* here; substitute your project's actual layout and commands.

---

## Table of Contents

1. [Philosophy & Principles](#philosophy--principles)
2. [Scope & Use](#scope--use)
3. [Related Documentation](#related-documentation)
4. [Authority Hierarchy & Read Order](#authority-hierarchy--read-order)
5. [Planning & Internal Documentation](#planning--internal-documentation)
6. [Session Contract: `codingprocess.md`](#session-contract-codingprocessmd)
7. [The `ref/` Directory](#the-ref-directory)
8. [Public Surface: `.public.txt`](#public-surface-publictxt)
9. [Status Surface: `.status.txt`](#status-surface-statustxt)
10. [Public Documentation Voice](#public-documentation-voice)
11. [Prompt & Task Shape](#prompt--task-shape)
12. [Session Lifecycle](#session-lifecycle)
13. [Verification & Evidence](#verification--evidence)
14. [Git, Branches, and Commits](#git-branches-and-commits)
15. [Where AI Excels](#where-ai-excels)
16. [Security & Trust](#security--trust)
17. [External Benchmarks](#external-benchmarks)
18. [What Not To Do](#what-not-to-do)
19. [Session Checklist](#session-checklist)
20. [Summary](#summary)

---

## Philosophy & Principles

### Core Tenets

1. **Contracts beat "vibes".** Behavior comes from specs and standards, not from chat memory or `phase.md`.
2. **Plan before you patch.** Hard work gets roadmaps, direction docs, and status ledgers before large code dumps — then **interplay** in small slices once you know what you are attacking.
3. **Evidence before "done."** If tests did not run, say so. If behavior is unknown, record it in `idk.md`.
4. **Small, honest slices.** One real improvement beats ten inflated status updates.
5. **Internal docs are internal.** Planning, study notes, and `ref/` material are not public-facing project docs, gang some of yall turn your repos into markdown soup and feed us AI text as if we gone read all dat.
6. **Short prompts, real verification.** Task text should read like a developer message, not a scraped GitHub issue novel.
7. **You own git by default.** You and the model edit files; **you** commit unless you explicitly told it it may commit this session.
8. **Process is mandatory.** `codingprocess.md` and project standards are constraints, not suggestions.
9. **Deviation is explicit.** When implementation diverges from plan for good reason, write it down — do not silently rewrite the spec.
10. **Public copy describes what ships.** No "we will eventually" in README, LICENSE, or operator docs unless status is the point.
11. **Status docs move with the work.** List living ledgers in **`.status.txt`** and update them when your triggers fire — stale `phase.md` after a green test run is a failed handoff.
12. **Right mode for the moment.** **Sit-and-plan** when the attack surface is unclear; **interplay** when a direction doc already shows the tree — side panel, skinny prompts, short replies, you stay in the driver's seat.

### Design Intent

These standards exist so AI-assisted work stays:

- aligned with my very opinionated `PROJECT_STANDARDS.md` and `CODE_STANDARDS.md` when those apply
- traceable across sessions via `codingprocess.md`, **`.status.txt`**, and honestly updated status docs
- safe to resume after context loss
- honest about unknowns instead of guessing semantics
- separated from public-facing prose and licensing
- free of commit spam, co-author theater, and leaderboard optimization
- usable both for long unattended passes **and** for in-editor interplay without the session turning into autopilot

This is a standards document, not a prompt cookbook.
If a rule needs to be broken, break it deliberately and document why.

Several rules below are shaped by published work on prompting, long-context use, code-generation security, and agent benchmarks. Primary sources are listed under [Standards Basis](#standards-basis) in the Summary — same pattern as `CODE_STANDARDS.md`.

---

## Scope & Use

This document is **not project-specific**.
It is the default AI workflow standard for repos that use the PROJECT_STANDARDS suite unless a project's `codingprocess.md` explicitly overrides something.

### The standards suite

| Document | Governs |
|----------|---------|
| [`PROJECT_STANDARDS.md`](./PROJECT_STANDARDS.md) | Branches, PRs, releases, **Conventional Commits**, contribution flow |
| [`CODE_STANDARDS.md`](./CODE_STANDARDS.md) | Code structure, testing, style (when the repo uses it) |
| [`AI_STANDARDS.md`](./AI_STANDARDS.md) | How you use AI: planning docs, prompts, git delegation, public/private boundaries |
| [`DECISION_STANDARDS.md`](./DECISION_STANDARDS.md) | D-points, audit markers, how architectural calls are recorded |
| [`PLANNING_STANDARDS.md`](./PLANNING_STANDARDS.md) | Shape of `plan.md` and planning artifacts |
| **Per-repo `codingprocess.md`** | Session handoff, read order, non-negotiables for that project |

If instructions conflict:

1. **Project/spec contract** (`SPEC.md`, `INTENT.md`, `spec/`, etc.) wins on behavior.
2. **`codingprocess.md`** wins on session procedure for that repo.
3. **`PROJECT_STANDARDS.md`** wins on git/PR workflow.
4. **`CODE_STANDARDS.md`** wins on code quality when present.
5. **`AI_STANDARDS.md`** fills gaps for AI-assisted workflow.
6. Chat messages do **not** override written standards.

### Start here

Before a substantial AI-assisted slice:

1. Open the repo's **`codingprocess.md`** (create one if the project is mid-size or larger) and feed or point the model at it.
2. Have **`PROJECT_STANDARDS.md`** handy if git is in play.
3. Have **`CODE_STANDARDS.md`** if the project uses it.
4. Load authority docs in the **order** `codingprocess.md` lists (`SPEC.md`, `phase.md`, …).
5. Check **`.status.txt`** when the project uses one — you should know which status files to touch when the slice lands.
6. Skim relevant source and tests yourself; don't outsource orientation to the model.

### When this document applies

- Any Cursor/Codex/CLI agent session on your repos
- Unattended long passes ("keep going until blocked")
- Documentation-heavy work (plans, audits, direction, study notes)
- **Interplay** — side panel, quick keybinding session, autocomplete top-off, "line this file into `main.rs`"
- Optional delegated commits (see [Git, Branches, and Commits](#git-branches-and-commits))

Small one-off questions do not need a full planning stack.
A multi-file feature, language implementation, or refactor does.

### Two workflow modes

Most of this document describes the **planning pass** playhouse: authority order, gates, `.status.txt`, honest ledgers, full handoffs. That is the right default for ambiguous or large work.

**Interplay** is the other playhouse squeezed into the same rules: you are already in the weeds (Neovim, IDE, terminal), you know the subsystem from a **study** or **direction** doc, and you use AI as a **narrow instrument** — not a second project manager.

| | **Sit-and-plan** | **Interplay** |
|---|------------------|---------------|
| **When** | New subsystem, unclear behavior, big refactor, unattended pass | Direction doc exists; you know the file tree; one function, one CLI flag, one wiring job |
| **Planning artifact** | `plan.md`, roadmap, direction doc, gates | **`implementation-plan`** for *this slice only* (chat, scratch file, or `impl-<slice>.md`) |
| **Prompt** | Full [contractor shape](#good-task-prompt-shape) | [Skinny interplay shape](#interplay-prompt-skinny) |
| **Reply size** | Full handoff; update status docs | **Short** — what changed, what you do next |
| **Status docs** | Update `.status.txt` paths when slice lands | Often **later** — you merge status when the slice actually closes |
| **Who drives** | Model can run a slice if you delegated | **You drive**; model tops off, wires, finishes from context |

Use sit-and-plan until you can name the slice. Then interplay.

---

## Related Documentation

Other suite documents (normative, project-agnostic):

- [`PROJECT_STANDARDS.md`](./PROJECT_STANDARDS.md) — branches, PRs, Conventional Commits
- [`CODE_STANDARDS.md`](./CODE_STANDARDS.md) — code structure and testing
- [`DECISION_STANDARDS.md`](./DECISION_STANDARDS.md) — D-points and audit markers
- [`PLANNING_STANDARDS.md`](./PLANNING_STANDARDS.md) — `plan.md` shape

Illustrative document shapes for planning, session contracts, `.public.txt` / `.status.txt`, and boundaries are **in this file** under each section — fictional projects **Harbor** (application) and **Meridian** (language/toolchain). Copy the shapes into your repo; Harbor and Meridian are placeholders, not repos you maintain.

---

## Authority Hierarchy & Read Order

Models do not reliably use information buried in the middle of a long context window. Liu et al. (*Lost in the Middle*, TACL 2024) show a U-shaped retrieval curve: relevant text at the **start or end** is used far more reliably than the same text in the middle. A **fixed read order** is worth enforcing in how you prompt — not superstition.

When you prompt:

- Put **non-negotiables** (standards, security, “do not invent behavior”) at the **top** of what you send.
- Repeat **done-when, verification commands, and stop conditions** at the end of long prompts or when you ask for a handoff.
- Don't paste a 400-line file naked — lead with a short index (goal, authority files, done-when).

### Generic authority stack

| Layer | Typical files | Role |
|-------|---------------|------|
| **Session law** | `codingprocess.md` | Handoff contract you give the model for this repo |
| **Workflow law** | `PROJECT_STANDARDS.md` | Branches, PRs, commits |
| **Code law** | `CODE_STANDARDS.md` | Implementation quality |
| **Behavior contract** | `SPEC.md`, `INTENT.md`, `spec/lang/`, … | What the software must do |
| **Status / roadmap** | `phase.md`, `CODEBASE_AUDIT.md`, direction docs (listed in **`.status.txt`**) | What is landed, missing, blocked |
| **Uncertainty** | `idk.md` | Open behavior questions only |
| **Deviation** | `*deviation.md`, project-specific name | Intentional plan/spec drift with rationale |
| **Planning** | `plan.md`, study docs, phase/direction notes | How and when to implement — **not** behavior |

**Forbidden promotions:**

- `phase.md` → behavior spec
- Chat summary → behavior spec
- SWE-bench issue text → task prompt template
- Internal planning prose → public README/LICENSE

### Per-repo read order

Every serious project should declare startup order in **`codingprocess.md`**. Two common shapes:

**Application or service (e.g. fictional “Harbor” deploy tool):**

1. `CODE_STANDARDS.md`
2. `SPEC.md` — runtime/spec contract
3. `INTENT.md` — UX intent / operator model (if split from spec)
4. `phase.md`
5. `CODEBASE_AUDIT.md`
6. `idk.md`
7. Relevant `src/` and tests

**Language, compiler, or spec-heavy repo (e.g. “Meridian” language):**

1. `PROJECT_STANDARDS.md` and `CODE_STANDARDS.md`
2. `phase.md`
3. `ACCEPTED.md` or equivalent accepted-shape ledger
4. `spec/` — normative rules (when written)
5. `DIRECTION.md` — implementation direction only
6. Relevant compiler/stdlib sources and tests

For large edits, ask the model to **say which authority files it read** before it patches — catches skipped `SPEC.md` / `phase.md`.

---

## Planning & Internal Documentation

Hard problems deserve **staged thinking on disk** before code. Wang et al. (*Plan-and-Solve Prompting*, ACL 2023) show that explicit **plan → execute** beats a bare “think step by step” on multi-step reasoning benchmarks, with fewer missing-step failures. Your `plan.md`, direction docs, and `phase.md` queues are that separation applied to engineering — not endless markdown for its own sake.

The split between `SPEC.md` (behavior), `phase.md` (status), `CODE_STANDARDS.md` (implementation law), and `codingprocess.md` (session procedure) also matches software-engineering work on prompts as **versioned, testable artifacts** (e.g. *Promptware Engineering*, Casanova et al. 2025; *Impromptu*, SoSyM 2024): ambiguous natural language needs structure, not one giant blob.

### Document types

| Artifact | Purpose | Is behavior spec? |
|----------|---------|-------------------|
| **`plan.md`** | Scope, build order, open decisions ([`PLANNING_STANDARDS.md`](./PLANNING_STANDARDS.md)) | No |
| **`phase.md`** | Status ledger: done / in progress / blocked with evidence paths | No |
| **Direction doc** | Implementation direction for a large area (e.g. `*direction.md`, `*languagedirection.md`) | No — cites spec/decided shape |
| **Study / research note** | Investigation, prior art, tradeoffs (e.g. `*study*.md`) | No |
| **Roadmap / phase planning** | Multi-step release planning (e.g. `release-3-planning.md`) | No |
| **`idk.md`** | Unanswered **behavior** questions only | No — feeds spec/process |
| **Deviation doc** | Recorded gap between plan/spec and chosen approach | No — explains intentional drift |
| **`CODEBASE_AUDIT.md`** | Honest map of what exists vs missing | No |
| **`DECISION_STANDARDS` D-points** | Architectural forks with IDs | Decisions, not implementation |
| **Implementation plan** | Temporary slice plan: files, steps, done-when for *one* attack | No — thrown away or archived after the slice |

### Implementation plan (interplay slice)

After a **study** or **direction** doc exists, you already know the tree shape (which crates, which modules, which gates). For one concrete job — fix Clap help output, wire a new file into `main.rs`, finish a function autocomplete mangled — ask the model for an **implementation-plan**, then execute (yourself, with interplay, or both).

The plan is **narrow**: not a replacement for `plan.md` or `DIRECTION.md`.

Example prompt:

```markdown
Read `DIRECTION.md` § Checker frontend and `src/cli/args.rs`.
Write an implementation-plan only (no code yet) for: align `--help` with SPEC §2.4 flag table.
List: files to touch, order of edits, test command, done-when.
```

Example plan output shape (may live in chat or `impl-clap-help.md`):

```markdown
# Implementation plan — Clap help alignment (Harbor CLI)

**Authority:** SPEC §2.4, `src/cli/args.rs`, `tests/cli_help.rs`
**Out of scope:** subcommand help for `inspect` (separate slice)

## Steps
1. Add missing long flags to `Args` struct; mirror names in `help_template.md` fragment.
2. Regenerate snapshot test `tests/cli_help.rs` if intentional text change.
3. Run `cargo test -p harbor-cli --test cli_help`.

## Done-when
- `cli_help` snapshot green; manual `harbor --help` matches SPEC table ordering.
```

Then interplay: spawn side session, skinny prompt, short reply, you keep typing in the main buffer.

### When to create or update what

| Situation | Update |
|-----------|--------|
| New large feature or subsystem | `plan.md` or direction doc + `phase.md` row |
| Behavior unclear | `idk.md` — **never** guess in code |
| Implementation path differs from written plan but still matches spec | Deviation doc + short note in `phase.md` |
| Landed code + tests | Every path in **`.status.txt`** that applies (at minimum `phase.md`; audit if map changed) |
| Accepted semantics change | Spec / D-point — not only `phase.md` |

If the repo has **`.status.txt`**, treat it as the manifest of **which** status files are in scope — not an optional reminder.

### `idk.md` rules

- Entries are **questions**, not implementation guesses.
- If `SPEC.md` (or equivalent) answers it, close the entry — do not duplicate spec text.
- Add to `idk.md` when you (or the model) would otherwise have to **guess** behavior.

Example entry shape (fictional Harbor project):

```markdown
## IDK-3: Remote cache TTL after failed publish

**Status:** open
**Area:** runtime
**Raised while implementing:** `src/sync/publish.rs`
**Question:** After a failed publish, does Harbor retry with the same cache key or rotate TTL?
**Current evidence:** SPEC.md §4.2 says publish is atomic; silent retry behavior not stated.
**Risk if guessed:** Operators see duplicate artifacts or stale cache hits.
**Needed decision:** Maintainer confirms retry + cache contract.
**Resolution:** —
```

### Deviation document rules

When mid-implementation you discover a **better** path that still respects the behavior contract:

1. **Do not** silently rewrite `phase.md` into a new spec.
2. **Do** append a row or section to the project's deviation file: what differed, why it is valid, what proof/tests still owe.
3. If the change **changes behavior**, stop — that is `idk.md` or a D-point, not a deviation note.

Deviation docs let you fix local mistakes without corrupting the plan/spec stack.

Example deviation row (fictional Meridian proof layer — plan used abstract `Step`, implementation keeps surface `stage` names only):

| Subject | Normative rule | Plan / proof treatment | Why valid | Still owed |
| --- | --- | --- | --- | --- |
| Surface `stage` keyword | Programs use `stage` for structured concurrency regions. | Proof core uses abstract `Step` nodes; no surface `stage` token in calculus. | Proof needs named steps without importing full surface syntax. | Conformance: show `stage` elaborates to `Step` without changing region bounds. |

### Direction document (example header)

For large subsystems, a direction doc records **how** accepted behavior lands — not **what** behavior is.

```markdown
# Meridian — Checker Direction

**Status:** Active implementation direction
**Primary authority:** ACCEPTED.md
**Normative authority:** spec/types.md (when extracted)
**Execution roadmap:** phase.md

This file does not decide new semantics. If this file and ACCEPTED.md disagree,
ACCEPTED.md wins until the rule is written in spec/.
```

### Phase status row (example)

```markdown
| Phase | Status | Last Updated | Notes |
|-------|--------|--------------|-------|
| Harbor remote sync | In Progress | 2026-06-01 | `tests/sync_publish.rs` green; CLI flags pending SPEC alignment |
| Meridian borrow checker | Blocked | 2026-06-01 | Blocked on IDK-3; no spec text for reborrow across `stage` |
```

### Roadmap planning snippet (example)

Internal note for a future release — **not** a behavior contract. Roadmaps name **goals**, **out of scope**, **done-when**, and **gates**: which global checkpoints from `phase.md` this release prepares, blocks on, or may close. Do not treat a roadmap slice as complete because code exists — a gate stays open until its done condition in `phase.md` is satisfied.

```markdown
# Release 3 Planning — Harbor

**Status:** Draft planning only — does not override SPEC.md
**Global gates:** defined in `phase.md` § Global Gates (authoritative)

## Goal
Ship read-only remote inspection before write-back publish.

## Out of scope for R3
- Multi-tenant auth (R4)
- Windows agent (deferred)

## Gate map (R3)

| Gate | R3 relationship | Done condition (summary) |
|------|-----------------|---------------------------|
| Gate A — Spec closure for inspect | **Must close** for R3 | SPEC §6 inspect contract frozen; no open `idk.md` on inspect semantics |
| Gate B — Read-only remote path | **Must close** for R3 | `harbor inspect` never mutates remote state; audit tests prove it |
| Gate C — Write-back publish | **Prepares only** | R3 must not claim publish; blocked until Gate C closes in R4 |

**Gating rules for this release:**
- No R3 “shipped” row in `phase.md` until Gates A and B are **closed** with evidence paths.
- Subsystems may run in parallel only when their gate annotations say they are not `BLOCKED_ON_GATE`.
- Saying R3 “closes Gate B” requires the exact Gate B checklist in `phase.md`, not a green unit test alone.

## Done-when (R3 slice)
- `harbor inspect --remote <name>` lists manifests per SPEC §6
- Integration test `tests/remote_inspect.rs` passes on clean VM
- `phase.md` Gate A and Gate B rows show **Closed** with test/doc paths

## Sequencing
1. Close Gate A (spec + `idk.md` clear for inspect)
2. Implement inspect path under Gate B constraints
3. Land tests → close Gate B → then mark R3 complete in `phase.md`
```

### Implementation stages (workflow)

**Sit-and-plan** (mid-to-large):

```text
Study / audit (optional) → plan or direction doc → phase/status updates
    → implementation-plan (per slice) → code + tests → verify → status honest → you commit / PR
```

**Interplay** (in the weeds, slice already named):

```text
Direction doc in head → implementation-plan (optional, 30s) → skinny prompt → small edit chunk
    → quick report → you continue → repeat → status docs when slice actually lands
```

You can have the model draft planning markdown for big passes.
Don't let it jump straight to code on "hard think" work because you rushed the chat.
In interplay, insist on a **short report** each chunk — you are mid-edit and need to know what it touched.

---

## Session Contract: `codingprocess.md`

`codingprocess.md` is the per-repo **handoff you give the model** (same niche as `AGENTS.md`; this suite uses the name on purpose). Write it **to the model**; keep `AI_STANDARDS.md` as your manual.

Every repo that gets regular AI development should have one at the repo root or path named in project docs.

### Minimum contents

- **Purpose** — what the file is for
- **Non-negotiables** — mandatory standards paths
- **Startup read order** — numbered list
- **Source-of-truth table** — which file answers which question
- **Status update matrix** — what to update when work lands (must align with **`.status.txt`** when present)
- **Status update triggers** — when updates are mandatory (e.g. after tests green, end of slice, subsystem touch, hook-fired)
- **`.status.txt` path** — where the manifest lives if not session cwd
- **Testing commands** — what to run before claiming done
- **Blockers** — when to stop vs continue
- **Communication rules** — concise, no cheerleading, list verification
- **Interplay rules** (optional) — side-panel behavior, chunk size, when status docs are deferred

Example skeleton (fictional Harbor repo — **paste into `codingprocess.md`**, addressed to the model; trim or extend):

```markdown
# Harbor — AI Session Contract

**Purpose:** Handoff rules for agents working in this repository.

## Non-negotiables

- `PROJECT_STANDARDS.md`, `CODE_STANDARDS.md`, `AI_STANDARDS.md` (if adopted)
- `SPEC.md` for all behavior
- No git commits unless the user delegated commits this session

## Startup read order

1. This file
2. `.status.txt` (if present at session cwd)
3. `CODE_STANDARDS.md`
4. `SPEC.md`
5. `phase.md`
6. `idk.md`
7. `src/` and `tests/` for the task area

## Status manifest (`.status.txt`)

Living docs listed in `.status.txt` **must** be updated when triggers below fire.
Do not mark a slice done in chat without touching every applicable status file.

| Trigger | Update |
|---------|--------|
| `cargo test` green for a harbor slice | `phase.md` row + evidence path |
| New crate or removed module | `CODEBASE_AUDIT.md` map section |
| Plan path changed, spec still holds | `deviations.md` + `phase.md` note |
| End of session or unattended slice | Re-read `.status.txt`; reconcile all listed files |

Optional: repo hook or CI check that fails if `src/` changed but `phase.md` did not (project choice).

## Source of truth

| Question | File |
|----------|------|
| What must the project do? | `SPEC.md` |
| What is done / blocked? | `phase.md` |
| What is undefined? | `idk.md` — do not guess |
| How to structure code? | `CODE_STANDARDS.md` |

## Verification

    cargo test -p harbor-core
    cargo test -p harbor-cli --test remote_inspect

Report command output or explain why not run.

## Blockers

Stop and ask when: SPEC silent, `idk.md` open on the task, or tests fail after a focused fix attempt.

## Communication

Concise. No cheerleading. List files touched, commands run, blockers.

## Interplay (side panel / quick session)

When the operator is driving in-editor (e.g. leader+g → AI panel):

- Read only what the prompt names + open buffer context; do not reload the whole authority stack unless asked.
- **One slice per invocation** — wire file, finish function, draft scope for upcoming edit.
- Reply in **≤15 lines**: files touched, what changed, next step for the operator, command if run.
- Do **not** refactor adjacent code, update `phase.md`, or narrate philosophy unless asked.
- If behavior is unclear, stop in one sentence and point at `idk.md` — do not guess.
```

Point every session at the repo's `codingprocess.md` when it exists.
If chat drifts from that file, the file wins — that's why you maintain it.

---

## The `ref/` Directory

On **mid-to-large** projects, maintain a workspace **`ref/`** directory (or equivalent) for material that informs AI work but **is not part of what you ship**.

### What belongs in `ref/`

- Copies or drafts of standards and `codingprocess.md` templates
- Example `idk.md`, deviation, direction, and phase planning docs
- Study notes, market research, internal critiques
- Session exports, benchmark notes, tooling comparisons
- Anything you want in context without polluting the shipping tree

### What to keep in mind

- `ref/` content is **reference-only** unless a `codingprocess.md` explicitly promotes a path to authority.
- Do **not** import `ref/` paths into public docs, runtime strings, or LICENSE files.
- Do **not** treat `ref/` as automatically committed — see [Git](#git-branches-and-commits).

Store **drafts** of the shapes in this document (`codingprocess.md`, `idk.md`, deviation tables, direction headers) under `ref/` while iterating — then promote finished copies to repo-root authority paths.
Do not link `ref/` from public docs or ship it as public surface unless explicitly listed in `.public.txt`.

---

## Public Surface: `.public.txt`

`.public.txt` is a manifest of paths that are **public** (operator-facing, publishable, or license-bound).

### Placement

Put `.public.txt` at the **top of the working directory where you work with AI**, not only at a distant monorepo root if your session cwd is deeper.

Example: if you work in `apps/harbor-cli/`, the manifest for that public surface may live **there** so rules apply to the docs you actually edit.

Format: one path per line; directories imply all children public unless excluded in comments.

Example `.public.txt` (fictional Meridian public language docs):

```text
# Public surface — Meridian language (operator + contributor docs only)
README.md
LICENSE
docs/
spec/surface-syntax.md
# Internal: ref/, idk.md, codingprocess.md, phase.md — not listed
```

### Rules

- Public files **must not** link to, name, or imply non-listed internal paths (`ref/`, `idk.md`, internal audits, `codingprocess.md`, planning dirs).
- Public files **must not** use workstation absolute paths (`/home/...`, `/1tb/...`).
- When promoting a file to public, add it to `.public.txt` in the same change.
- When you or the model edit public docs, **read** `.public.txt` first when the project uses one.

This is how internal instructional docs stay out of README, man pages, and LICENSE.

---

## Status Surface: `.status.txt`

`.status.txt` is the counterpart to `.public.txt`: a manifest of **living status documents** you keep aligned when work changes — not behavior specs, but honest maps of what landed, what is blocked, and what drifted.

Without a manifest, models forget `CODEBASE_AUDIT.md` after fixing `phase.md`, or ship code while direction docs still describe last month's plan.

### Placement

Same rule as `.public.txt`: put **`.status.txt` at the session cwd** where you work (repo root or `apps/harbor-cli/`, etc.), not only at a distant monorepo root.

### Format

One path per line. Comments (`#`) document **triggers** and scope. Directories imply all markdown children are status docs unless excluded.

Example `.status.txt` (fictional Harbor):

```text
# Living status docs — mandatory updates per codingprocess.md
phase.md
CODEBASE_AUDIT.md
deviations.md
# trigger: after cargo test green for harbor-core or harbor-cli
# trigger: when crates/ membership changes
# trigger: end of every slice before you stop for the day
```

Example `.status.txt` (fictional Meridian):

```text
phase.md
DIRECTION.md
deviations.md
ACCEPTED.md
# trigger: after dune runtest in meridian-checker
# trigger: when spec/ or proofs/ layout changes
```

### What belongs in `.status.txt`

| Usually list | Usually omit |
|--------------|--------------|
| `phase.md`, `CODEBASE_AUDIT.md` | `SPEC.md` (behavior contract, not a ledger) |
| Direction docs (`DIRECTION.md`, `*direction.md`) | Static standards (`CODE_STANDARDS.md`) |
| Deviation files | `idk.md` unless you treat open questions as session status |
| Accepted-shape ledgers that track implementation progress | `ref/` drafts |
| Internal roadmaps you want updated when slices land | Public paths (use `.public.txt`) |

Add a file when you or the model repeatedly **forget** to update it after code changes.

### Wiring updates (pick one or combine)

| Mechanism | What it does |
|-----------|----------------|
| **`codingprocess.md` triggers** | Human-readable table: event → which `.status.txt` paths to touch (required minimum) |
| **Your default** | You (or the model, if `codingprocess.md` says so) update listed paths before you call a slice done |
| **Cursor / git hooks** | Pre-commit or stop hook runs a script: if `src/` changed, require `phase.md` diff (project-maintained) |
| **CI check** | Optional job fails when status files are stale vs listed globs (heavier, good for unattended passes) |

`codingprocess.md` should **name the triggers explicitly** — vague "keep docs updated" does not survive context loss. Examples:

- **After verification green** — update `phase.md` with test path and date
- **After tree layout change** — refresh `CODEBASE_AUDIT.md` section for that area
- **After plan-path drift** — append deviation doc + one-line `phase.md` note
- **End of slice / session** — re-read `.status.txt` and reconcile every listed file

Hooks and CI are optional; updating listed status files when triggers fire is not.

### In practice

- Open **`.status.txt`** at the start of a planning pass (right after `codingprocess.md` is fine).
- Before you call a slice **done**, touch every listed path that applies — or note what you skipped and why.
- Don't list `SPEC.md` in `.status.txt` to justify behavior changes; spec updates follow spec process, not status hygiene.
- Don't publish `.status.txt` paths in public docs unless they also appear in `.public.txt` (they should not).

### New status doc

When you add a moving ledger (e.g. `release-3-planning.md` becomes active), add it to `.status.txt` and add a row to `codingprocess.md`'s status update matrix in the same change.

---

## Public Documentation Voice

AI is strong at drafting docs; it is also strong at **leaking process into public copy**. Separate the two.

### Default rule

Public docs describe the software **as it exists for the reader now**, not the team's internal journey.

| Write this | Not this |
|------------|----------|
| "The CLI supports `install` and `upgrade`." | "We are implementing install next sprint." |
| "Status: experimental" (if status is the subject) | "TODO: fix later" scattered in user guides |
| Stable API and behavior | Internal phase names, D-point IDs without local rule text |
| Links only to `.public.txt` paths | "See `release-3-planning.md` for details" (internal planning) |

### Exceptions

- **Explicit status sections** (roadmap, changelog, maturity tables) may describe in-progress work if the document's job is status.
- **CONTRIBUTING / PROJECT_STANDARDS** may describe process — they are not operator manuals.

### LICENSE and legal prose

- No AI session narration, no "we might change," no references to internal planning files.
- Factual, release-aligned, lawyer-readable if applicable.

### AI-assisted doc workflow

Good uses:

- Sync README/USAGE with behavior after a verified change
- Fix examples, typos, formatting (often `docs/*` PR to `main` per `PROJECT_STANDARDS.md`)
- Cross-check public docs against `.public.txt`

Bad uses:

- Dumping `phase.md` into README
- Exposing `codingprocess.md` or `ref/` to end users
- Future-tense instructions in LICENSE

---

## Prompt & Task Shape

### Why short, behavior-focused prompts

Public coding benchmarks often ship **GitHub-issue-shaped** task text: long reproduction steps, implicit file targets, and prescriptive test policy. That is a bad template for day-to-day prompts.

Empirical and audit evidence :

- **Contamination / memorization:** Yang et al. (*The SWE-Bench Illusion*, 2025) and follow-on localization studies report models guessing buggy file paths from issue text alone at rates that collapse on held-out repos — leaderboard gains partly reflect **training exposure**, not transferable skill. OpenAI (2026) stopped reporting SWE-bench Verified after finding frontier models reproduce **gold patches** and verbatim problem details.
- **False passes:** Yang et al. (*SWE-Bench+*, 2024) audited “successful” agent runs and found large fractions involved **solution leakage** in issue threads or **weak tests** that accepted wrong patches; filtered resolve rates dropped sharply. OpenAI’s Verified audit found material test/description flaws in a majority of hard cases reviewed.
- **Bad prompt + sandbox habits:** DeepSWE benchmark audits (Datacurve, 2026) report SWE-Bench Pro prompts averaging roughly **twice** the character count of their behavior-focused tasks while expecting far **less** code change; Pro templates often tell agents **not** to modify tests, which suppresses self-verification; evaluation containers that retain full **git history** enabled a substantial share of “passes” via reading merged gold commits ([SWE-bench_Pro-os#93](https://github.com/scaleapi/SWE-bench_Pro-os/issues/93)).

**Policy:** Task prompts for your repos should look like a message to a competent contractor — outcome, authority pointers, done-when — not like a scraped issue export. Jimenez et al. (ICLR 2024, SWE-bench) remains useful background that **repo-level** agent work is hard; **Verified percentage is not** a trustworthy scoreboard for picking tools.

### Good task prompt shape

```markdown
**Goal:** (one paragraph — behavior outcome)

**Authority:** Read `codingprocess.md`, then SPEC.md §X, then `src/foo/`.

**Constraints:** Follow CODE_STANDARDS.md. No git commits unless I said so.

**Done when:** `cargo test -p bar` passes; `phase.md` row N updated with test path.

**Out of scope:** …
```

Properties:

- **Short** — intent + pointers, not a full spec paste
- **Behavior-focused** — what should work, not which file to edit first
- **Authority links** — where truth lives
- **Done-when** — commands and artifacts
- **Explicit git policy** — commit or not

### Interplay prompt (skinny)

Use when you are **mid-edit** and a direction doc already says *where* in the tree. Same idea as the contractor shape above, stripped down — paste into a side panel or quick session:

```markdown
**Slice:** Wire `src/sync/cache.rs` into `main.rs` re-exports; no behavior change.
**Read:** `src/main.rs`, new file only.
**Do:** minimal diff; run `cargo check -p harbor-cli`.
**Reply:** files changed, what you did, what I should do next (≤15 lines). No commits.
```

Good interplay asks:

- "Finish this function from selection" / "line up module in `lib.rs`"
- "Write implementation-plan for SPEC §6 inspect flags only — plan only"
- "Top off error handling for this match arm; I will review before save"

Bad interplay asks:

- paste entire `SPEC.md` with no slice
- "refactor the CLI while you are here"
- silent multi-file rewrite with no chunk report

### Dual work (you + AI on one slice)

When you and the model are **paired** on the same slice (not a fire-and-forget side panel):

- Ask for **small chunks** — one logical edit set, then stop and report before the next.
- You review between chunks; don't let it queue five unrelated tasks in one go.
- Still get an **implementation-plan** if the slice spans more than ~2 files or touches behavior.
- Update status docs and run full verification when the **slice closes**, not after every micro-edit.

### Role prompts

Prefer **process roles** ("follow `CODE_STANDARDS.md`") over **identity roles** ("you are a world-class Rust genius").

Role-play can help on some reasoning benchmarks (Xu et al., NAACL 2024), but systematic studies on factual and knowledge-heavy tasks often show **no gain or harm** from generic “expert” personas — and MMLU-style work suggests persona prefixes can push models into instruction-following mode at the expense of recall. For coding and spec work, **constraints and authority files** beat cosplay.

---

## Session Lifecycle

Pick a mode first: [sit-and-plan](#planning-pass-lifecycle) or [interplay](#interplay-lifecycle). Planning pass is the default for big or fuzzy work. Interplay relaxes how much you load up front and how long replies can be — **not** spec authority and not guessing behavior.

### Planning pass lifecycle

#### 1. Before you dig in

- Know cwd and branch (if git is in play).
- Open `codingprocess.md`, **`.status.txt`** (if present), and the authority stack for this slice.
- Tell the model what to read (or paste the read order from `codingprocess.md`).
- For a named slice, get an **implementation-plan** first if the edit is large.

#### 2. Work loop

```text
Read → implementation-plan (slice) → edit → run tests/commands → update status docs → continue or stop
```

- Have it **run tools** (read file, test, patch) — don't accept "done" without output. Yao et al. (*ReAct*, ICLR 2023) is the research backing for interleaving reasoning and commands.
- Keep slices small; push back on drive-by refactors.
- Put unknowns in `idk.md`; put plan drift in the deviation doc when spec still holds.
- When **`.status.txt`** triggers fire, update listed status files before you call the slice done.

#### 3. End of session (what you should get back)

Ask for a handoff that includes:

- Files changed (paths)
- Commands run and pass/fail
- Which status docs were updated
- What's next: **now** / **blocked on you** / **deferred**
- Whether it committed (only if you allowed that)

Don't treat the slice as done without verification evidence you can skim.

#### Long unattended passes

When you tell it to continue until blocked:

- Make it reread **`.status.txt`** and listed status docs before it stops.
- It should not quit after one arbitrary milestone if safe work remains (`codingprocess.md` pattern).
- It should stop on: missing credentials, behavior that needs your call, external dependency, or your explicit stop.

### Interplay lifecycle

Interplay is when you are **already implementing**, a direction doc (or study) answers "where in the tree," and you only need execution help. Typical setup: editor keybinding → small AI panel (e.g. leader+g in Neovim); Cursor side chat; another terminal tab — tool doesn't matter, **habit** does.

#### 1. Before a quick ask

- You already know cwd and target files.
- Prompt names **only** what to read plus selection/open buffers — skip reloading the whole authority stack unless behavior is in play.
- Optional: 30-second **implementation-plan** in chat before the first edit on multi-file slices.

#### 2. Work loop

```text
You edit → skinny ask → small change → short reply → you continue
```

What to ask for in the prompt (put this in `codingprocess.md` if you want it enforced):

- **One job per invocation** — wire a module, fix help text, draft a scope block, finish a function from selection.
- **Small diffs** — if it needs more than ~3 files or ~80 lines, it should stop and you widen scope or switch to planning pass.
- **No silent batching** — you want a chunk report between edit sets, not five files at once with no summary.
- Autocomplete gaps count ("finish this `match` arm from selection").
- `phase.md` / audit can wait until **you** close the slice unless you said "update status too."

What you do:

- Know interplay vs sit-and-plan; escalate when lost or behavior is undefined.
- Point at direction-doc tree paths (`frontend/lexer/`, `cli/args.rs`) instead of re-explaining the repo.

#### 3. Reply you want back (interplay)

Not an essay. **≤15 lines**, roughly:

```markdown
**Did:** Added `mod cache` in `main.rs`; re-exported `CachePolicy` from `sync/cache.rs`.
**Ran:** `cargo check -p harbor-cli` (ok).
**You next:** Fill `run()` body in `cache.rs` line 42 — I left `todo!()`.
**Skipped:** `phase.md` (say if you want it updated).
```

You stay in flow without reading a transcript.

#### When to leave interplay

Switch to planning pass when:

- behavior needs `idk.md` or a spec change
- the slice needs gates / roadmap / a new direction doc
- the model whiffed twice on the same small ask
- you are still **discovering** architecture, not executing a known slice

### Choosing the mode (quick)

```text
Have direction doc + named slice?     → interplay (+ implementation-plan if ≥2 files)
New area / spec silent / gates?     → sit-and-plan
You said "keep going until blocked"? → planning pass only
Side panel / "just wire this"?      → interplay
You + model paired on one feature?  → dual work: short chunks; full handoff when slice closes
```

---

## Verification & Evidence

**Done** means artifacts, not adjectives.

### Correctness epistemology

Chen et al. (*Evaluating Large Language Models Trained on Code*, 2021) established **pass@k** against unit tests as the right measure for code generation: correctness is **binary per test suite**, and multiple samples beat trusting one greedy output. That is why this suite insists on **project test commands** (`cargo test`, `dune runtest`, etc.) — not “looks right” and not public leaderboard %.

Where feasible, use **two verification paths** (tests + lint, or tests + targeted manual check). Wang et al. (*Self-Consistency*, ICLR 2023) show multi-sample agreement improves hard reasoning; in engineering that translates to not declaring green from a single flaky run.

| Claim | Required evidence |
|-------|-------------------|
| Tests pass | Command output or explicit "not run because …" |
| Behavior implemented | Test path or spec section + code path |
| Docs accurate | Public paths only; match `.public.txt` |
| Phase item complete | `phase.md` cites file/test path — not "mostly done" |
| Status hygiene | All applicable **`.status.txt`** paths updated or explicitly deferred with reason |

### Generated code is untrusted by default

- Pearce et al. (IEEE S&P 2022): ~**40%** of Copilot completions in CWE-oriented scenarios were vulnerable.
- Perry et al. (2023): participants **with** AI assistants wrote **less secure** code than those without, while **believing** they were more secure; more deliberate prompt iteration correlated with fewer vulnerabilities.

Review, tests, and explicit constraints are not paranoia — they counter measured failure modes.

### Human validation is the bottleneck

Vaithilingam et al. (CHI 2022) found Copilot did not reliably improve task completion time or success in a controlled study, even though most participants still wanted it — friction was **understanding, editing, and debugging** generated code. Your handoff rules (concise diff summary, commands run, blockers named) target that bottleneck, not cheerleading.

---

## Git, Branches, and Commits

### Default policy: you commit

**Do not let the model run `git commit` unless you explicitly allowed commits for that session.**

Why:

- Commits are attribution and history; you did not type every line.
- `PROJECT_STANDARDS.md` assumes review, branches, and PR flow you actually use.
- Models over-commit (every save) and write essay messages.

When commits are **not** allowed:

- It edits files; **you** run git.
- A useful model still suggests branch name and Conventional Commit subject in chat.

When commits **are** allowed, everything in this section applies.

### Before any commit

1. **`git status`** and **`git branch --show-current`** — know where you are.
2. **Correct branch** per `PROJECT_STANDARDS.md`:
   - Code: `feat/*`, `fix/*`, … from `dev`
   - Docs-only public: `docs/*` from `main` → PR to `main`
3. **New work → new branch** — do not commit unrelated changes on someone else's feature branch unless asked.
4. **No destructive git** — no `push --force`, `reset --hard`, etc., unless user explicitly requests.

### When to commit (not every file touch)

| Do commit (batch) | Do not commit |
|-------------------|---------------|
| Logical slice complete (tests green or stated partial) | After every single file save |
| User asked for a commit | Each internal planning typo |
| End of session with delegated commit policy | "WIP" spam on shared branches without user consent |

Internal markdown (`phase.md`, `plan.md`, `ref/`, audits, `idk.md`) accumulates during work — **batch** one `docs:` or `chore:` commit per session slice, or leave uncommitted for you to squash.

**Never** commit secrets (`.env`, keys, tokens).

### Conventional Commits (mandatory when committing)

Follow [`PROJECT_STANDARDS.md` § Commit Discipline](./PROJECT_STANDARDS.md#commit-discipline) exactly:

```text
type(optional-scope): short description

[optional body]

[optional footer]
```

Examples:

```bash
feat(harbor): add remote manifest diff for publish retries
fix(parser): reject unclosed borrow scope
docs(readme): sync install flags with SPEC
chore: update phase tracker after sync slice
```

| Type | Use for |
|------|---------|
| `feat` | New behavior |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `refactor` | Behavior-preserving restructure |
| `test` | Tests only |
| `chore` | Tooling, deps, internal trackers |

**Subject line:** imperative, short, no period.  
**Body:** explain *why*, not play-by-play of every file.  
**No** "Applied patch", "Updated files per user request", or model self-narration.

### Co-authored-by (when the model commits)

If the model creates the commit, you did not author every line. Require a footer:

```text
Co-authored-by: Cursor Agent <cursoragent@cursor.com>
```

Use the tool name the user actually uses (`Co-authored-by: Codex …`, etc.) if they specify it.
If the project already documents a bot co-author line, use that.

Do not pretend you solo-authored AI-generated patches.

### What to get back when you delegated a commit

In chat, it should state:

- Branch name
- Full commit subject (and body if non-trivial)
- That `Co-authored-by` was included
- What was **not** committed (if anything left unstaged)

### PR flow reminder

Commits on a feature branch are not the end — `PROJECT_STANDARDS.md` expects PR to `dev` (or docs PR to `main`).
Don't let it merge unless you explicitly said so.

---

## Where AI Excels

Use models where they pay rent:

| Strong fit | Weak fit |
|------------|----------|
| Drafting and restructuring **internal** plans, audits, direction docs | Inventing behavior without `idk.md` / spec update |
| **Public docs** drafts from verified behavior + `.public.txt` | Public docs from `phase.md` or vibes |
| Boilerplate tests, golden updates after you define the contract | Security-critical crypto/auth without review |
| Refactors with `CODE_STANDARDS.md` + tests | "Optimize" without measurement |
| Cross-file exploration and status reconciliation | Trusting SWE-bench % as readiness |
| `idk.md` and deviation hygiene | Commits without explicit permission |
| **Interplay** top-offs: wire modules, finish functions, skinny implementation-plans | Interplay without chunk reports or full-repo refactors in a side panel |
| Slice **implementation-plans** after direction docs exist | Implementation-plans that replace `SPEC.md` or skip gates |

**Documentation** is the highest-signal use: AI drafts, you verify against spec and public boundary, then ship via normal docs workflow.

---

## Security & Trust

- Generated code may be vulnerable by default — review and test.
- Users with AI assistants may write **less secure** code while feeling **more** secure ([Perry et al., 2023](https://arxiv.org/abs/2211.03622)).
- More prompt iteration and less blind trust correlates with better outcomes in that study — write constraints down.

Don't let it disable hooks, skip tests, or bypass review unless you explicitly asked for a defined local experiment.

---

## External Benchmarks

Do not tune prompts, tools, or internal process to maximize **SWE-bench Verified** (or any single public SWE headline). Use external numbers only with the failure modes in [Prompt & Task Shape](#prompt--task-shape) in mind.

### What public SWE scores do and do not mean

| Use for | Do not use for |
|---------|----------------|
| Arguing repo-level agent tasks are structurally hard (Jimenez et al., ICLR 2024) | Proving your agent stack or repo is production-ready |
| Motivating tests + sandbox discipline | Picking Cursor vs Codex vs “best model” from a saturated leaderboard |
| Explaining why GitHub-issue prompt paste is harmful | Replacing `codingprocess.md` + local `cargo test` |

### Multi-axis comparison (when you must compare vendors)

[Artificial Analysis Coding Agent Index](https://artificialanalysis.ai/methodology/coding-agents-benchmarking) deliberately combines **patch** work (SWE-Bench-Pro-Hard-AA), **terminal** workflows (Terminal-Bench v2), and **repository Q&A** (SWE-Atlas-QnA) with pass@1 and repeated runs — because one task shape does not equal “coding ability.” That is the right *culture* for external comparison even though Pro-family slices may still carry contamination and harness issues.

**Minimum bar for any external claim:** component breakdown + cost/tokens/time + **your repo’s** smoke tests on real branches.

### Positive prompt-shape reference (benchmark methodology)

DeepSWE (Datacurve, 2026) is a vendor benchmark, not peer-reviewed canon — treat it as **methodology evidence**, not gospel scores. It is still the best recent public articulation of prompt shape this suite endorses:

- behavior-focused, developer-register prompts
- shorter mean prompt length than SWE-Bench Pro in their published comparison
- behavior verifiers that accept multiple correct implementations
- no “do not touch tests” clause — strong models write tests more often
- original tasks to reduce pretraining collision

Align your prompts and `codingprocess.md` with **that shape**, not with Pro issue exports.

---

## What Not To Do

1. **Commit without permission** or commit on every file change.
2. **Omit `Co-authored-by`** on model-authored commits you delegated.
3. **Use non-Conventional Commit** subjects when committing.
4. **Commit on wrong branch** (e.g. code on `main`, docs on wrong base).
5. **Promote `phase.md` to spec** or implement from roadmap alone.
6. **Guess behavior** — use `idk.md` or stop.
7. **Hide deviation** — update deviation doc when plan path changes.
8. **Reference internal paths in public docs** or LICENSE.
9. **Write public docs in future tense** unless status is the document's job.
10. **Paste SWE-bench issue templates** as prompts.
11. **Claim tests passed** without running them or stating why not.
12. **Inflate status** ("done" without artifact paths).
13. **Leave `.status.txt` docs stale** after code or test changes.
14. **Cheerlead, coach, or narrate** instead of reporting evidence.
15. **Persona spam** ("world-class expert") instead of standards references.
16. **Treat `ref/` as shippable** public surface.
17. **Use planning-pass handoffs in interplay** — keep side-panel replies short.
18. **Batch silent edits** in dual work — report each chunk before the next.
19. **Interplay without a named slice** when the direction doc does not tell you where in the tree.

---

## Session Checklist

### Planning pass (before stopping or handing off)

- [ ] Mode: sit-and-plan (not fire-and-forget side panel)
- [ ] Read `codingprocess.md` and authority files for this task
- [ ] Behavior changes match spec — unknowns in `idk.md`
- [ ] Tests/commands run or failure explained
- [ ] Read `.status.txt` when present; all listed status docs updated per triggers
- [ ] `phase.md` / audit / deviation / direction honest (not only `phase.md` when audit also applies)
- [ ] Public edits respect `.public.txt` and present-tense public voice
- [ ] Git: only if allowed — correct branch, Conventional Commit, `Co-authored-by`
- [ ] No secrets committed
- [ ] Handoff lists files, verification, blockers, remaining work

### Interplay (after each panel chunk or when pausing)

- [ ] Slice was named; direction doc or implementation-plan covered *where*
- [ ] Reply ≤15 lines: did / ran / you next / skipped
- [ ] No drive-by refactors or spec guesses
- [ ] Escalate to planning pass if behavior unclear or slice grew past ~3 files

---

## Summary

### Core references

- Workflow & commits: [`PROJECT_STANDARDS.md`](./PROJECT_STANDARDS.md)
- Code quality: [`CODE_STANDARDS.md`](./CODE_STANDARDS.md)
- Planning shapes: sections above (Harbor / Meridian examples)

### One-line policy

**Sit-and-plan when the attack is unclear; interplay in small chunks when direction already shows the tree. Maintain `codingprocess.md` for the model; keep `.status.txt` honest when a slice lands; verify with commands; keep public/private separate; you own git unless you delegated commits with Conventional Commits and co-author attribution.**

### Standards Basis

Primary references used to shape this document:

- Liu et al., *Lost in the Middle: How Language Models Use Long Contexts* (TACL 2024)  
  https://arxiv.org/abs/2307.03172
- Wang et al., *Plan-and-Solve Prompting* (ACL 2023)  
  https://arxiv.org/abs/2305.04091
- Wei et al., *Chain-of-Thought Prompting* (NeurIPS 2022)  
  https://arxiv.org/abs/2201.11903
- Yao et al., *ReAct: Synergizing Reasoning and Acting in Language Models* (ICLR 2023)  
  https://arxiv.org/abs/2210.03629
- Chen et al., *Evaluating Large Language Models Trained on Code* (2021)  
  https://arxiv.org/abs/2107.03374
- Wang et al., *Self-Consistency Improves Chain of Thought Reasoning in Language Models* (ICLR 2023)  
  https://arxiv.org/abs/2203.11171
- Jimenez et al., *SWE-bench: Can Language Models Resolve Real-World GitHub Issues?* (ICLR 2024)  
  https://arxiv.org/abs/2310.06770
- Yang et al., *SWE-Bench+: Enhanced Coding Benchmark for LLMs* (2024)  
  https://arxiv.org/abs/2410.06992
- Yang et al., *The SWE-Bench Illusion* (2025)  
  https://arxiv.org/abs/2506.12286
- OpenAI, *Why SWE-bench Verified no longer measures frontier coding capabilities* (2026)  
  https://openai.com/index/why-we-no-longer-evaluate-swe-bench-verified/
- Datacurve, *DeepSWE* methodology and audits (2026)  
  https://deepswe.datacurve.ai/blog
- Artificial Analysis, *Coding Agent Index Methodology* (2026)  
  https://artificialanalysis.ai/methodology/coding-agents-benchmarking
- Pearce et al., *Asleep at the Keyboard?* (IEEE S&P 2022)  
  https://doi.org/10.1109/SP46214.2022.9833571
- Perry et al., *Do Users Write More Insecure Code with AI Assistants?* (2023)  
  https://arxiv.org/abs/2211.03622
- Vaithilingam et al., *Expectation vs. Experience* (CHI 2022)  
  https://arxiv.org/abs/2202.07606
- Xu et al., *Better Zero-Shot Reasoning with Role-Play Prompting* (NAACL 2024)  
  https://aclanthology.org/2024.naacl-long.228/
- Casanova et al., *Impromptu* (Software and Systems Modeling, 2024)  
  https://link.springer.com/article/10.1007/s10270-024-01235-4
- Casanova et al., *Promptware Engineering* (2025)  
  https://arxiv.org/abs/2503.02400
- Sarkar et al., *What is it like to program with artificial intelligence?* (2022)  
  https://advait.org/publications-web/sarkar-2022-programming-ai/

---

End of Standard
