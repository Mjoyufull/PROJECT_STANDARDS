# Codebase Audit Template

**Version:** 1.0.0  
**Date:** 2026-03-25

This dictates how we run codebase audits. Audits are brutally honest snapshots aimed at contributors trying to get their bearings. They aren't roadmaps, they aren't wishlists, and they aren't for bashing people. We use them to establish exactly what's real, what's half-baked, what's entirely stubbed, and what's on fire.
i currently use these in many forms outside of development bc i tend to work on many projects

---

## How To Use

1. Copy this into your repo as `CODEBASE_AUDIT.md`.
2. Back up every statement with hard evidence from the code.
3. Keep it updated when big merges land.
4. Never claim something works without dropping a file path, line number, or test name to prove it.

---

## Template

```markdown
# [Project Name] Codebase Audit

**Audit date:** YYYY-MM-DD  
**Scope:** [What this audit covers]

---

## 1. Overview

[One to three paragraphs. What the project is, its actual current state, and the most accurate mental model. Be brutally honest about the ugly parts.]

### 1.1 Snapshot Delta (YYYY-MM-DD)

[Bulleted list of the highest-signal changes since the previous audit snapshot. Only include changes that affect a reader's mental model of the project.]

---

## 2. Repository Layout

[Directory tree showing the relevant structure. Annotate with purpose comments.]

---

## 3. Architecture Snapshot

### 3.1 High-Level Block Diagram

[ASCII or Mermaid diagram showing major components and their relationships.]

### 3.2 Key Runtime Paths

[Describe the 1-3 most important runtime paths (e.g. request flow, boot sequence, data pipeline). For each, list the files involved in order.]

---

## 4. Crate / Module Tree

[For Rust workspaces: list workspace members with one-line purpose. For other languages: equivalent module/package listing.]

---

## 5. Data Model

[Core tables, schemas, or persistent state structures. Include state enums and their transitions.]

---

## 6. What Is Implemented

[Bulleted or tabled list of features/capabilities that are real and working. Group by subsystem.]

---

## 7. What Is Partial, Placeholder, or Stubbed

[These are the landmines that will surprise a new contributor. List everything that looks more complete than it actually is. For each item, state exactly what exists and what is missing.]

---

## 8. Explicit TODOs and Inferred Roadmap

### 8.1 Explicit TODOs in Code

[Table of high-signal TODOs with file, line, and meaning.]

| Location              | TODO            | Meaning                |
| --------------------- | --------------- | ---------------------- |
| `path/to/file.rs:123` | Brief TODO text | What it actually means |

### 8.2 Inferred Missing Capabilities

[Things the architecture suggests should exist but do not. These are the gaps between what the types/interfaces promise and what the runtime actually does.]

### 8.3 Likely Implementation Queue

[If you had to infer the most logical next-work order from the code, what would it be? Numbered list.]

---

## 9. Issues

### Severity Legend

| Severity     | Meaning                                                                                          |
| ------------ | ------------------------------------------------------------------------------------------------ |
| **Critical** | Correctness or resource-integrity problem that can break runs or leave the system in a bad state |
| **High**     | Feature appears to work but is incomplete, misleading, or fragile in production                  |
| **Medium**   | Design debt or robustness problem that will slow contributors or cause avoidable failures        |
| **Low**      | Cleanup issue, warning, or mismatched surface unlikely to cause immediate failure                |

### [ID]-01: [Issue Title]

**Severity:** [Critical / High / Medium / Low]  
**Location:** `path/to/file.rs:line`  
**Status:** [Open / Fixed / Deferred]

**Problem:** [What is wrong]

**Impact:** [What happens because of this]

**Resolution:** [What was done to fix it, or "Open"]

[Repeat for each issue. Use a project-specific prefix for IDs, e.g. MBX- for Malbox, KDX- for Kaleidux, Y- for Yuuko.]

---

## 10. Structural Concerns

[Patterns that aren't technically bugs but will cost contributors time. Architecture mismatches, misleading APIs, drift between types and actual runtime behavior.]

---

## 11. Contributor Read Order

[Ordered list of files a new contributor should read to build an accurate mental model, fastest path first.]

---

## 12. Contribution Tracks

[Suggested first-contribution tracks for new contributors, ordered by leverage. Each track should have deliverables and a brief rationale.]

---

## 13. Verification Log

[What verification was performed during this audit: build checks, test runs, manual inspection.]

---

## Audit Maintenance Rules

1. Update it after every big merge.
2. Every status claim needs code evidence. Drop the file path.
3. Don't mark research or hardware-gated items as failures.
4. Keep issue IDs stable across versions (don't renumber them just because you fixed one).
5. Put snapshot deltas at the top (§1.1) so people actually see them.
```

---

## Variations by Project Type

### Performance-Oriented Projects (e.g. Kaleidux)

Add a **Performance Audit** section between §9 and §10 with:

- Severity legend specific to performance (Critical/High/Medium/Low/OK).
- Per-issue format including file, impact, resolution, and "cannot be improved" constraints.
- An "Already Well-Optimized" subsection documenting what does not need work.
- A summary issue tracker table.

### Spec-Compliance Projects (e.g. ProjectYuuko)

Add a **Spec Compliance Matrix** section using:

- HDB bands (H0–H5) for difficulty classification.
- Status values: `Implemented`, `Partial`, `Planned`, `Deferred (Research/HW)`, `Unverified`.
- Feature flag registry documenting all configurable behaviors.
- Configuration-stage feature availability matrix.
- Hardness-ordered implementation path.

### Critique-Oriented Audits (e.g. Elda)

Use P-level markers for issues and decision points, with the option/resolution format from `DECISION_STANDARDS.md`.

---

## Changelog

### v1.0.0 — 2026-03-25

- Initial audit template, synthesized from Malbox, Kaleidux, and ProjectYuuko audit structures.
