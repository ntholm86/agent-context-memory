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

---

## 2026-06-20 — initial-retrospect

**Ask:** Generate work queue from destination using retrospect skill.

**Scope:** Read full arc (2 entries) against destination. Derive what must be built to fulfill the destination.

**Arc-read:**
- Target is in scaffolding phase — naming settled, spec not started
- Destination defines 4 sections SPEC.md must cover: tiers, structural requirements, mandate gate, convergence
- Prior art is named but not formally cited
- README is complete; SPEC.md is empty

**Decision:** Write retrospect.md with 6-item work queue covering each spec section the destination requires.

**Action:** Created .trail/retrospect.md with:
- 5 current claims about target state
- 6-item work queue (the spec sections)
- 4 active operational rules
- Loop-effectiveness deferred (arc too short)

**Reflection:** The mandate gate pattern is working — destination existed, was read, interpretation is now visible in this trace entry. Work queue is now explicit. Next: begin SPEC.md with Section 1 (The Three Tiers).
