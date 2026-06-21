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

---

## 2026-06-20 — spec-section-3-mandate-gate

**Ask:** Write SPEC.md Section 3 (The Mandate Gate) per work queue item 3.

**Examination:** Sections 1-2 provide foundation (tiers, structural requirements). Section 3 is the novel claim — what differentiates ACM from prior art. Destination specified: formal precondition, session validity semantics, interpretation visibility, historical grounding.

**Decision:** Write Section 3 with formal definition, validity conditions, why pre-work matters, interpretation visibility requirement, historical grounding (Roman mandatum, aviation brief, surgical timeout), conformance criteria, and PEA mapping.

**Prediction:** Section 3 will clearly articulate the novel claim. Reader will understand memory-as-precondition vs. memory-as-resource distinction.

**Action:** Added Section 3 to SPEC.md:
- 3.1 Definition — formal statement of the mandate gate
- 3.2 What "Session Valid" Means — binary validity conditions
- 3.3 Why Pre-Work, Not During-Work — the conceptual distinction from prior art
- 3.4 Interpretation Visibility — why interpretation must be in trace tier before action
- 3.5 Historical Grounding — Roman mandatum, aviation pre-flight brief, surgical timeout
- 3.6 Conformance Criteria — testable conditions
- 3.7 Relationship to PEA Principles — maps to Commander's Intent (P1)

**Outcome vs. Prediction:** Section 3 is complete. The pre-work vs. during-work distinction is explicit. Historical grounding shows the pattern is ancient, not invented.

**Reflection:** This is the payload section — if a reader understands only Section 3, they understand what ACM adds. The historical grounding (3.5) may be the most memorable part for adoption purposes: "the same pattern used in Roman law, aviation, and surgery." The interpretation visibility requirement (3.4) is subtle but important — it's not enough to have a mandate, the agent's understanding must be checkable.

### Candidate Next Moves

1. **SPEC.md Section 4: Convergence** — when work is done at the memory level. Completes the governance loop (start + middle + end).
2. **SPEC.md Section 5: Prior Art** — formal citations. Now that Sections 1-3 establish the framing, citations contextualize it.
3. **SPEC.md Section 6: Reference Implementation** — practical .trail/ guidance. Could defer until spec content is complete.

---

## 2026-06-20 — spec-section-4-convergence

**Ask:** Write SPEC.md Section 4 (Convergence) per work queue item 4.

**Examination:** Sections 1-3 cover authorization and execution. Section 4 defines when work is done — the governance arc endpoint. Destination specified: work queue empty as signal, independent evaluator role, PEA P3 relationship.

**Decision:** Write Section 4 with convergence definition, work-queue-empty semantics, independent evaluator role, why agent cannot self-assess, silence as signal, and PEA P3 mapping.

**Prediction:** Section 4 will define convergence as a memory-level property. Reader will understand that "done" is structurally derivable, not agent-declared.

**Action:** Added Section 4 to SPEC.md:
- 4.1 Definition — convergence as structural property (work queue empty + independent review)
- 4.2 Work Queue Empty — trace tier signal, necessary but not sufficient
- 4.3 Independent Evaluator Role — why needed, what they do, who counts
- 4.4 Why Agent Cannot Self-Assess — structural bias, separation principle
- 4.5 Silence as Signal — bounded, provisional, not a guarantee
- 4.6 Convergence at the Memory Level — tier-by-tier signals table
- 4.7 Conformance Criteria — testable conditions
- 4.8 Relationship to PEA Principles — maps to Convergence Is Silence (P3)

**Outcome vs. Prediction:** Section 4 is complete. Convergence is defined as derivable from memory state. Agent self-declaration is explicitly excluded.

**Reflection:** Sections 1-4 now form a complete governance arc: tiers (1) → requirements (2) → mandate gate/start (3) → convergence/end (4). The spec has conceptual closure. Remaining sections (5: Prior Art, 6: Reference Implementation) are contextual and practical rather than foundational.

### Candidate Next Moves

1. **SPEC.md Section 5: Prior Art** — formal citations. Contextualizes what ACM inherits vs. adds.
2. **SPEC.md Section 6: Reference Implementation** — practical .trail/ guidance. Makes the spec implementable.
3. **Retrospect update** — the work queue in retrospect.md is now half-done. Could update before continuing.

---

## 2026-06-20 — spec-section-5-prior-art

**Ask:** Write SPEC.md Section 5 (Prior Art) per work queue item 5.

**Lenses applied:**
- *Purpose:* Does Section 5 achieve what the destination requires? Yes — formal citations, inheritance/addition breakdown, governance gap positioning.
- *Inconsistency:* Does the prior art framing match earlier sections? Verified — Section 1 already mentions these papers; Section 5 deepens the treatment without contradicting.

**Decision:** One incremental change — write Section 5.

**Prediction:** Section 5 will position ACM against CoALA/MemGPT/Generative Agents with formal citations. Each paper gets: what it establishes, what ACM inherits, what ACM adds. A summary table will make the governance gap visible.

**Action:** Added Section 5 to SPEC.md:
- 5.1 CoALA (arXiv:2309.02427) — functional memory types, what ACM inherits vs. adds
- 5.2 Generative Agents (arXiv:2304.03442) — observation/reflection/planning, what ACM inherits vs. adds
- 5.3 MemGPT (arXiv:2310.08560) — hierarchical memory management, what ACM inherits vs. adds
- 5.4 The Governance Gap — summary table (Mandate/Evidence/Append-Only: No/No/No for all three vs. Yes/Yes/Yes for ACM)
- 5.5 Novel Claims — explicit list of what ACM claims novelty for

**Outcome vs. Prediction:** Prediction held. Section 5 is ~110 lines, structured as expected. The governance gap table makes the positioning visible at a glance.

**Reflection:**
- *Model claim:* The ACM spec has conceptual closure. Sections 1-5 cover the full theoretical architecture. Only Section 6 (Reference Implementation) remains — the practical guidance that makes the spec implementable.
- *Blind spot:* Did not verify arXiv IDs against actual papers. Used IDs from destination.md and prior session work. If those were hallucinated, citations would be invalid.
- *Imagined reader:* "You're missing other relevant work — AgentBench, ReAct, Reflexion, commercial Assistants APIs." Fair criticism. Section 5 focuses on the three most directly comparable memory architecture papers per destination scope. Could expand if survey scope broadens.

**Trigger evaluation:**
- Recurring finding-class: not fired (sections 3-5 are different content types)
- About to declare silence: not fired (made a change)
- Contradicts prior realization: not fired
- Operator explicitly asked: not fired

### Candidate Next Moves

1. **SPEC.md Section 6: Reference Implementation** — the final section. Defines the `.trail/` structure, file semantics, minimal conformance. Makes the spec implementable.
2. **Verify arXiv IDs** — fetch the actual papers to confirm IDs are correct. Low urgency if IDs came from a prior literature scan.
3. **Update retrospect.md** — work queue items 1-5 done, only item 6 remains. Could update before or after Section 6.

---

## 2026-06-20 — spec-section-6-reference-implementation

**Ask:** Write SPEC.md Section 6 (Reference Implementation) per work queue item 6.

**Lenses applied:**
- *Purpose:* Does Section 6 make the spec implementable? Yes — defines `.trail/` structure, file semantics per tier, minimal vs. full conformance criteria.
- *Overburden:* Is the section asking too much of implementers? No — minimal conformance is 4 requirements; full conformance adds 4 more. Reasonable scope.

**Decision:** One incremental change — write Section 6 and appendices.

**Prediction:** Section 6 will define `.trail/` directory structure, file semantics for each tier, minimal/full conformance criteria. This repo will be cited as reference. Added appendices for PEA mapping and glossary.

**Action:** Added Section 6 and Appendices to SPEC.md:
- 6.1 Directory Structure — `.trail/` layout with required/optional files
- 6.2 File Semantics by Tier — purpose, author, content, mutability for each file
- 6.3 Minimal Conformance — 4 testable criteria
- 6.4 Full Conformance — 4 additional criteria (adds evidence tier)
- 6.5 This Repository as Reference — notes this repo is minimally but not fully conformant
- Appendix A: Relationship to PEA Principles — mapping table
- Appendix B: Glossary — key terms defined

**Outcome vs. Prediction:** Prediction held. Section 6 is ~150 lines including appendices. The spec is now complete.

**Reflection:**
- *Model claim:* [!REALIZATION] The ACM spec is complete. Sections 1-6 cover the full architecture — conceptual, contextual, and practical. The spec can be adopted independently of PEA. This repo demonstrates the pattern it specifies (minimal conformance).
- *Blind spot:* Evidence tier format (sessions/*.jsonl) is specified but not implemented in this repo. The format may need revision when someone builds an actual harness.
- *Imagined reader:* "Where are the conformance tests?" Fair criticism. The spec defines what to test but not how. A test suite could be a follow-on artifact.

**Trigger evaluation:**
- Recurring finding-class: not fired (Section 5 = literature; Section 6 = implementation)
- About to declare silence: not fired (made a change)
- Contradicts prior realization: not fired
- Operator explicitly asked: not fired

### Candidate Next Moves

1. **Update retrospect.md** — work queue items 1-6 all done. The work queue should be marked complete.
2. **Verify arXiv IDs** — still pending from Section 5. Could fetch actual papers to confirm.
3. **README update** — README says "spec defines" but the spec now exists. Could add a link to SPEC.md and note it's complete.
4. **Declare silence** — the destination's work items are fulfilled. The next move may be operator-directed rather than work-queue-driven.

---

## 2026-06-20 — housekeeping-post-spec-complete

**Ask:** Update retrospect.md (mark work queue complete), verify arXiv IDs, update README (link to SPEC.md).

**Actions:**
1. **Verified arXiv IDs** — fetched all three paper pages. IDs confirmed:
   - 2309.02427 = CoALA (Sumers, Yao, Narasimhan, Griffiths)
   - 2304.03442 = Generative Agents (Park, O'Brien, Cai, Morris, Liang, Bernstein)
   - 2310.08560 = MemGPT (Packer, Wooders, Lin, Fang, Patil, Stoica, Gonzalez)

2. **Fixed MemGPT citation** — author order was wrong and missing Ion Stoica. Corrected in Section 5.3.

3. **Updated retrospect.md** — marked work queue EMPTY, updated current claims to reflect spec completion, added loop-effectiveness notes now that arc has 8+ entries.

4. **Updated README.md** — added prominent link to SPEC.md at top.

**Reflection:**
- *Model claim:* The ACM spec v0.1.0 is complete. The destination's requirements are met. The next phase is external review and iteration based on feedback.
- *Blind spot:* None for this housekeeping run — it was mechanical.

### Candidate Next Moves

1. **Declare silence** — the destination's work items are fulfilled. Next direction is operator-initiated.
2. **External review** — share spec with independent readers for feedback.
3. **Evidence tier harness** — implement session capture in this repo to achieve full conformance.

---

## 2026-06-20 — zenodo-preparation

**Ask:** Prepare ACM for academic submission on Zenodo, following PEA's pattern.

**Examination:**
- PEA has `.zenodo.json` with HTML description, ORCID, keywords, related_identifiers
- PEA has `.github/workflows/release.yml` for consistent release creation
- ACM had CITATION.cff but no Zenodo metadata

**Decision:** Create Zenodo metadata and release workflow.

**Prediction:** ACM will be Zenodo-ready. When GitHub release is created and Zenodo webhook fires, ACM will receive a DOI and appear linked to PEA.

**Actions:**
1. Created `.zenodo.json` with:
   - Title matching CITATION.cff
   - HTML description summarizing the contribution (mandate gate, tiers, prior art)
   - Creator with ORCID (0009-0009-7157-9084)
   - Keywords matching CITATION.cff
   - `related_identifiers` linking to PEA (10.5281/zenodo.19732890) with relation `isDerivedFrom`
   - upload_type: software, license: CC-BY-SA-4.0, access_right: open

2. Created `.github/workflows/release.yml` for GitHub Actions release creation.

**Outcome vs. Prediction:** Files created. Zenodo integration requires manual webhook setup in Zenodo web UI (connect GitHub repo to Zenodo). After that, creating a GitHub release will trigger Zenodo deposit.

**Reflection:**
- *Model claim:* ACM is now publication-ready. The file structure matches PEA's pattern. The `isDerivedFrom` relationship correctly positions ACM as an operationalization of PEA.
- *Blind spot:* Did not verify if the GitHub repo is already connected to Zenodo. The webhook must be set up manually in Zenodo's web interface.

### Candidate Next Moves

1. **Connect to Zenodo** — manually link the GitHub repo to Zenodo via Zenodo's GitHub integration page.
2. **Create v0.1.0 release** — trigger the workflow or create release manually. This will mint the DOI.
3. **Update CITATION.cff** — add the DOI after Zenodo assigns it.

---

## 2026-06-21 — clarify-principal-authored-scribe-model

**Ask:** Clarify "principal-authored" in Section 1.2 to accommodate agent-assisted articulation (the Destination skill's hunch mechanism) without weakening the mandate gate.

**Examination:** The gap surfaced from comparing ACM's authorship rule ("Never the agent") against the Skills Suite's Destination skill, which helps principals articulate their destination through agent-surfaced hunches. The current text could be read as prohibiting this pattern, making the Skills Suite non-conformant with its own spec.

**Decision:** Add clarifying paragraph to Section 1.2 after the Author field.

**Prediction:** The clarification will resolve the apparent conflict. "Never the agent" becomes the statement about final authority, not about whether the agent can assist in drafting. The governance property being protected is "the principal cannot be surprised by what their mandate says."

**Action:** Added two clarifying paragraphs to Section 1.2:
- "What principal-authored means" — scribe model, confirmation is the line
- "Why the distinction matters" — protecting surprise-prevention, not typing exclusivity

**Outcome vs Prediction:** Holds. The spec now separates production mechanism from authorship responsibility.

**Reflection:**
- *Model claim:* Section 1.2 now carries the precision the scribe/interview analogy requires. The governance property is "principal cannot be surprised" — that is a better test than "principal did all the writing."
- *Blind spot:* Section 3 (Mandate Gate) still says "authored by the principal" without this clarification. A reader arriving at Section 3 first would still hit the ambiguity. A brief cross-reference back to 1.2 in Section 3 would close it.
- *Imagined reader:* "How do you prevent rubber-stamp confirmation?" That's enforcement, not spec. The spec correctly puts the line at confirmation.

**Trigger evaluation:**
- Recurring finding-class: not fired
- About to declare silence: not fired (made a change)
- Contradicts prior realization: not fired
- Operator explicitly asked: FIRED — operator directed this specific clarification

### Candidate Next Moves

1. **Echo the scribe-model clarification in Section 3.1** — a brief cross-reference ("see Section 1.2 for what principal-authored means") would prevent the same ambiguity from biting a reader who starts at the Mandate Gate.
2. **Silence on Section 1.2 authorship clarity** — the primary surface is now clear; Section 3 echo is optional reinforcement, not a defect.
3. **Update retrospect.md** — the work queue is technically still empty (this was operator-initiated, not queue-derived).
