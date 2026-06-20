# audit-trail.md — Agent Context Memory

Append-only decision log. Each entry records one iteration of work.

---

## 2026-06-20 — repo-initialization

**Ask:** Initialize the MCM repo with `.trail/` structure and capture destination before starting work.

**Examination:** Empty repo with `.git/` and empty `.trail/`. No prior work exists.

**Decision:** Create `destination.md` capturing the full scope: what MCM is, the novel claim (mandate gate), relationship to prior art (CoALA, MemGPT, Generative Agents), what the spec defines, and the quality bar.

**Action:** Created `.trail/destination.md` and `.trail/audit-trail.md`.

**Reflection:** The destination is now established. The mandate gate is in effect for this repo — the principal's intent exists before any spec work begins. Next: scaffold the spec itself.

---

## 2026-06-20 — rename-mcm-to-acm

**Ask:** Reconsider the name. "Mandated" front-loads a concept that requires explanation; the mandate gate is only part of the spec.

**Examination:** The naming pattern in PEA: the umbrella is "Principles of Earned Autonomy," not "Intent-Observable-Convergent Autonomy Framework." Commander's Intent is a principle inside, not the title.

**Decision:** Rename to **Agent Context Memory (ACM)**. The mandate gate becomes Principle 1 inside the spec rather than the title. This lets ACM be the umbrella covering all three tiers and all purposes, with the novel claim properly scoped inside.

**Action:** Renamed repo from `mandated-context-memory` to `agent-context-memory`. Updated destination.md and audit-trail.md to reflect ACM.

**Reflection:** ACM is cleaner. The name doesn't require explanation; it describes what it is. The novel contribution (mandate gate) is a principle inside the spec, same structural pattern as PEA.
