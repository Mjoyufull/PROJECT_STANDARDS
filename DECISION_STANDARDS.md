# Decision Standards

**Version:** 1.0.0  
**Date:** 2026-03-25

This sets the standard for how we actually make and track technical calls. The point is to stop relying on memory, make things evidence-based, and have a clear paper trail when we inevitably need to question a choice six months later.
i use these half the time tbh, but i should use them more, great for planning edge cases and just information in general

---

## 1. What Counts as a Decision

We are talking about choices that:

- Bleed across module or crate boundaries.
- Paint you into a corner for future work.
- Have multiple ways they could actually be built.
- Will absolutely make future-you ask "why the hell did I build it this way?"

Naming, formatting, and single-function logic aren't architectural calls. Keep that to `CODE_STANDARDS.md`.

---

## 2. Decision Marker Format

Every open or resolved call gets a unique ID so it can be tracked in the plan or audit docs.

### 2.1 In Plan Documents

Use a decision table in the "Open Decisions" section:

```markdown
| ID   | Question                       | Options                        | Leaning    | Status   | Resolution                |
| ---- | ------------------------------ | ------------------------------ | ---------- | -------- | ------------------------- |
| D-01 | Metadata serialization format? | TOML / JSON / RON              | TOML       | Open     | —                         |
| D-02 | Build isolation strategy?      | Namespaces / Bubblewrap / None | Namespaces | Resolved | Namespaces chosen, see §X |
```

### 2.2 In Critique / Audit Documents

Use P-level markers for problems and decision points discovered during audits:

```markdown
### P-01: [Problem Title]

**Severity:** Critical / High / Medium / Low  
**Location:** `path/to/file.rs:line`  
**Status:** Open / Resolved / Deferred

**Problem:** [What is wrong or unclear]

**Options:**

1. [Option A] — [tradeoffs]
2. [Option B] — [tradeoffs]
3. [Option C] — [tradeoffs]

**Resolution:** [What was decided and why, or "Open"]
```

### 2.3 Severity Legend

Use consistently across all documents:

| Severity     | Meaning                                                                |
| ------------ | ---------------------------------------------------------------------- |
| **Critical** | Blocks correctness, causes data loss, or makes the system unusable     |
| **High**     | Major capability missing, misleading surface, or fragile in production |
| **Medium**   | Design debt or robustness problem that will cost contributor time      |
| **Low**      | Cleanup issue, warning, or cosmetic problem                            |

---

## 3. How to Approach Decisions

When you hit a real fork in the road:

### 3.1 Frame the Actual Question

"What database should we use?" is a garbage question. "What storage backend handles CMA records with vector search and sub-millisecond local reads?" is an actual question. Be specific.

### 3.2 List the Contenders

Write down options that could actually work in reality. Don't waste time listing things you instantly ruled out, unless the reason you ruled them out is highly educational.

### 3.3 Hit Them Against Reality

For each option, check:

- **Feasibility:** Can we actually build this right now with what we have?
- **Fitness:** Does it solve the problem?
- **Cost:** What's the tax in complexity, deps, and maintenance?
- **Risk:** What's the worst-case explosion scenario?

### 3.4 Pick and Print

Pick the least-bad option. Document:

- What we chose.
- Why we chose it (keep it short).
- What we rejected and why.
- What specific future event would make us rip this out and try again.

---

## 4. Resolution Levels

Not every call needs a six-page document. Match the effort to the stakes:

| Level             | Stakes                                             | Process                                                     |
| ----------------- | -------------------------------------------------- | ----------------------------------------------------------- |
| **Trivial**       | Single file, easily reversible                     | Decide and move on. Drop a code comment if it's weird.      |
| **Local**         | Single crate or module                             | Note it in the plan or audit. Include one paragraph of why. |
| **Architectural** | Cross-crate, cross-subsystem                       | Full review (§3). Track it with a D-level marker.           |
| **Foundational**  | Language choice, database engine, core abstraction | Full review + let it sit and breathe before committing.     |

---

## 5. When to Backtrack

Nothing is written in stone. Turn around when:

- Reality shifted and the original constraints are gone.
- New benchmarks or production fires prove the old logic was wrong.
- The downstream effects of a choice are clearly causing too much pain.

Do **not** rip out architecture just because:

- Somebody has a subjective preference.
- Some new framework got posted on Hacker News.
- Reddit thinks your tech stack is uncool.

When you do backtrack, update the marker with the new reality. Don't erase history—append to it.

---

## 6. Trap Behaviors

### 6.1 Defaulting

Running with the first thing you typed without looking around. Fine for small shit, deadly for architecture.

### 6.2 Authority Bias

"Google uses this so we should." Google's problems are not your problems. Other people's constraints aren't yours.

### 6.3 Debate Fatigue

Picking an option just to shut someone up. If it's a deadlock, isolate the component, pick the cheapest path to validate, and move on.

### 6.4 Stalling

Leaving a decision open because everything has a downside. _Everything_ has a downside. Just pick your poison, document the pain, and execute.

---

## Changelog

### v1.0.0 — 2026-03-25

- Initial decision standards.
