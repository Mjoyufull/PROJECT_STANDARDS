# Planning Standards

**Version:** 1.0.0  
**Date:** 2026-03-25

This sets out how `plan.md` files should be built. A solid plan is a living doc that crushes the project's scope, decisions, and build order into something you can actually read in one sitting. It needs to stay updated after every decent merge.

---

## 1. Core Purpose

A plan exists to anchor reality. It answers:

- What exactly are we building?
- What are we *definitely not* building?
- What architectural calls are locked in?
- What's still up in the air?
- What order are we tackling this in?
- How do we know a phase is actually done?

It's not a tutorial, it isn't a pitch deck, and it shouldn't hold a thousand lines of implementation details. Put that in the code, comments, or the audit docs.

---

## 2. Plan Structure

Include these blocks in order. Skip them only if they are completely empty for your project.

### 2.1 Header Block

```markdown
# [Project Name] — Plan

**Version:** X.Y.Z  
**Date:** YYYY-MM-DD  
**Status:** [Draft | Active | Frozen | Archived]
```

- **Draft**: Plan is being written, not yet actionable.
- **Active**: Plan governs current development.
- **Frozen**: Plan is final for a completed phase or milestone.
- **Archived**: Plan is historical reference only.

### 2.2 Overview

One to three paragraphs max roughly describing what the project is. No marketing bullshit. No aspiration. Just the current reality and the exact target state.

### 2.3 Scope

#### In Scope
A list of concrete things we are actually delivering right now.

#### Out of Scope
Things we are explicitly ignoring. This is your shield against feature creep. If somebody asks "why doesn't it do X", the answer needs to be living right here.

### 2.4 Delivery & Risk

How are we rolling this out?
- **Rollout Strategy:** Binary swap, gradual migration, or hard cutover?
- **Reversibility:** How do we undo this if it explodes?
- **Known Tech Debt:** What ugly corners are we accepting for now just to ship?

### 2.5 Architecture & Shape

Explain the bones of the system. Block diagrams, crate trees, whatever makes sense. Keep it light enough to digest in a few minutes, but grounded enough to actually orient someone new.

### 2.6 Open Decisions

Track unresolved calls here using the D-level markers from `DECISION_STANDARDS.md`.

```markdown
| ID | Question | Options | Leaning | Blocker? |
|---|---|---|---|---|
| D-01 | Which serialization format for metadata? | TOML / JSON / RON | TOML | No |
| D-02 | Sandbox isolation strategy? | Namespaces / Bubblewrap / None | Namespaces | Yes |
```

### 2.7 Build Order

List the work phases. Break work down into small chunks to keep diffs short and reviews fast.

- **Name:** What we're calling this phase.
- **Scope:** What gets built. Keep these small (think "one concern per change").
- **Hardness:** H0–H5 band if you use HDB.
- **Dependencies:** What must land first.
- **Done-when:** Concrete proof it's finished.

```markdown
### Phase 1: Walking Skeleton
- **Scope:** CLI parses commands, reads config, resolves one package.
- **Hardness:** H0–H1
- **Dependencies:** None
- **Done-when:** `elda install hello-world` succeeds on a clean system.
```

### 2.8 Status Tracker

A summary table tracking the state of each phase or major deliverable.

```markdown
| Phase | Status | Last Updated | Notes |
|---|---|---|---|
| Walking skeleton | Done | 2026-03-10 | Core CLI + config + single resolve |
| Dependency resolver | In Progress | 2026-03-20 | SAT solver integrated, constraint propagation next |
| Build sandbox | Not Started | — | Blocked on D-02 |
```

Status values: `Not Started`, `In Progress`, `Done`, `Blocked`, `Deferred`.

### 2.9 Changelog

A reverse-chronological list of plan updates.

```markdown
### v1.1.0 — 2026-03-20
- Added Phase 3 (build sandbox) after D-02 resolved toward namespaces.
- Moved binary caching from Phase 2 to Phase 4.

### v1.0.0 — 2026-03-01
- Initial plan.
```

---

## 3. Maintenance Rules

1. **Update on merge.** If a PR hits `dev` and shifts the state of the project, update the plan.
2. **Back your claims.** "Done" means there's a file path or test proving it. "In Progress" means there's a branch. Stop lying to yourself in docs.
3. **Scope cuts only.** If scope has to grow, justify it loudly in the changelog.
4. **Kill stale decisions.** If a decision stays open for months, you either need to force an answer or explicitly sideline it.

---

## 4. Plan vs. Other Documents

| Document | Answers | Updated |
|---|---|---|
| `plan.md` | What are we building and in what order? | After meaningful merges |
| `CODE_STANDARDS.md` | How do we write code? | When standards evolve |
| `SOFTWARE_PHILOSOPHY.md` | Why do we make these choices? | Rarely |
| `DECISION_STANDARDS.md` | How do we document and resolve decisions? | When process evolves |
| `CODEBASE_AUDIT.md` | What is the actual state of the code? | After meaningful merges |

---

## Changelog

### v1.0.0 — 2026-03-25
- Initial planning standards.
