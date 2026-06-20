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

---

## 2026-06-20 — spec-section-1-three-tiers

**Ask:** Write SPEC.md Section 1 (The Three Tiers) per work queue item 1.

**Examination:** SPEC.md did not exist. Destination and README both contain tier tables. The spec needs to formalize: why trust level not memory type, each tier's semantics, authorship rules, conflict resolution.

**Decision:** Create SPEC.md with preamble (abstract) + Section 1 (The Three Tiers). One section per iteration keeps changes reviewable.

**Prediction:** SPEC.md will exist with formal tier definitions. README tier table will be consistent with spec.

**Action:** Created SPEC.md with:
- Abstract (what ACM is, novel claim, PEA relationship)
- Section 1: The Three Tiers
  - 1.1 The Tiers (table)
  - 1.2 Intent Tier (Mandate) — author, content, mutability, trust semantics
  - 1.3 Trace Tier (Decisions) — append-only, agent self-report
  - 1.4 Evidence Tier (Sessions) — capture-author separation, harness-authored
  - 1.5 Why Trust Level, Not Memory Type — comparison to prior art
  - 1.6 Conflict Resolution — how tiers interact when they disagree

**Outcome vs. Prediction:** SPEC.md exists. Section 1 is complete. README tier table is consistent (same three tiers, same trust order).

**Reflection:** Section 1 grounds the rest of the spec. The trust-vs-type distinction is the conceptual foundation — if a reader understands that, the structural requirements and mandate gate follow naturally. The conflict resolution subsection (1.6) may need refinement once we write Section 2 (structural requirements).

### Candidate Next Moves

1. **SPEC.md Section 2: Structural Requirements** — capture-author separation, append-only trace, trust-tiered conflict resolution. Builds directly on Section 1's tier definitions.
2. **SPEC.md Section 3: The Mandate Gate** — the novel claim. Could jump here if operator wants to front-load the differentiation.
3. **SPEC.md Section 5: Prior Art** — formal citations. Could do this early to establish the "what we inherit vs. what we add" framing.

---

## 2026-06-20 — spec-section-2-structural-requirements

**Ask:** Write SPEC.md Section 2 (Structural Requirements) per work queue item 2.

**Examination:** Section 1 defined tiers conceptually. Section 2 needed to formalize enforceable properties with conformance criteria. Destination listed three requirements: capture-author separation, append-only trace, trust-tiered resolution.

**Decision:** Write Section 2 with each requirement formally defined, rationale stated, and conformance criteria specified (testable conditions for compliance/non-compliance).

**Prediction:** SPEC.md will have Section 2 with testable structural requirements. An implementer can verify conformance against explicit criteria.

**Action:** Added Section 2 to SPEC.md:
- 2.1 Capture-Author Separation — definition, rationale, conformance criteria, reference implementation link
- 2.2 Append-Only Trace — definition, rationale, conformance criteria, git implementation note
- 2.3 Trust-Tiered Conflict Resolution — definition, rationale, conformance criteria, example
- 2.4 Requirement Summary — table + interdependency explanation

**Outcome vs. Prediction:** Section 2 is complete. Each requirement has explicit conformance/non-conformance criteria. An implementer can test against them.

**Reflection:** The conformance criteria make the spec auditable — you can check whether a system meets each requirement. The interdependency paragraph (2.4) explains why all three are needed together. Section 1.6 (conflict resolution) and Section 2.3 overlap slightly; could consolidate in a future pass, but not blocking.

### Candidate Next Moves

1. **SPEC.md Section 3: The Mandate Gate** — the novel claim, the reason ACM exists as a separate spec. Front-loads the differentiation.
2. **SPEC.md Section 4: Convergence** — connects to PEA Principle 3. Defines when work is done at the memory level.
3. **SPEC.md Section 5: Prior Art** — formal citations. Could defer until content sections are complete.
