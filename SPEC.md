# Agent Context Memory Specification

**Version:** 0.1.0 (draft)  
**Date:** 2026-06-20  
**Author:** Nils Wendelboe Holmager  
**License:** CC BY-SA 4.0

---

## Abstract

Agent Context Memory (ACM) is a specification for governance-first context memory in autonomous AI agent systems. It defines how agents should organize, protect, and use the memory that persists across sessions and governs their authorization to act.

ACM covers the full scope of agent memory — episodic, semantic, and working memory as prior work establishes — and adds a governance layer no prior specification defines. The novel contribution is the **Mandate Gate**: a pre-work mandate, authored by the principal, must exist in memory before any agent session is valid.

This specification is the operationalization of [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy) for the context memory design problem.

---

## 1. The Three Tiers

ACM organizes memory by **trust level**, not by memory type.

Prior agent memory models (CoALA, MemGPT, Generative Agents) organize memory by *function*: what kind of information is stored (episodic, semantic, procedural, working). This is useful for capability — it helps the agent retrieve and use information effectively.

ACM organizes memory by *trust*: who authored it, whether it can be modified, and how conflicts between tiers are resolved. This is useful for governance — it lets an observer determine whether the agent was authorized to act and whether its account of what it did can be trusted.

The two organizations are orthogonal. An ACM-conformant system may internally use functional memory types; the trust-tier organization is the governance layer that sits above them.

### 1.1 The Tiers

| Tier | Name | Trust Level | Author | Mutability | Purpose |
|------|------|-------------|--------|------------|---------|
| **Intent** | Mandate | Highest | Principal only | Append or replace (principal decision) | What the agent is authorized to do and why |
| **Trace** | Decisions | Medium | Agent | Append-only | What the agent decided and its reasoning |
| **Evidence** | Sessions | Independent | Harness | Immutable after capture | What actually happened (LLM calls, responses) |

### 1.2 Intent Tier (Mandate)

The intent tier contains the principal's mandate — the governing context that authorizes agent action.

**Author:** The principal — the party who holds responsibility for the content and confirms it as their mandate. The agent may contribute to articulation (surfacing hunches, drafting candidate text, asking clarifying questions), but the principal cannot be surprised by what their mandate says. Unconfirmed agent-drafted content is not the intent tier.

*The governance property being protected is not "the principal did all the typing" but "the principal owns and has confirmed the content."* Principals often have a richer interior model than they can immediately articulate; agent-assisted articulation mechanisms are conformant when the principal confirms the result.

**Content:** The destination the agent is working toward, the constraints that apply across all sessions, and any scope limitations. In the reference implementation, this is `destination.md`.

**Mutability:** The principal may update or replace the mandate at any time. The agent may not modify it.

**Trust semantics:** The intent tier is authoritative. When the agent's interpretation of a task conflicts with the mandate, the mandate wins. When the trace tier's account of what happened conflicts with what the mandate authorized, the mandate defines what *should* have happened.

**Why highest trust:** The principal is the party with standing to authorize action. The mandate is the only tier where the agent has no authorship — it cannot write, edit, or delete intent-tier content. This structural separation is what makes the tier trustworthy.

### 1.3 Trace Tier (Decisions)

The trace tier contains the agent's account of what it decided and why.

**Author:** The agent.

**Content:** Decision logs, reasoning traces, interpretations of the mandate, reflections, and derived artifacts like retrospects and work queues. In the reference implementation, this includes `audit-trail.md`, `retrospect.md`, `learning.md`, and `history.md`.

**Mutability:** Append-only. The agent may add new entries but may not modify or delete existing entries. Corrections are made by appending a new entry that references and supersedes the earlier one.

**Trust semantics:** The trace tier is the agent's self-report. It is useful but not independently verifiable. An observer reading the trace tier knows what the agent *claims* it did and why, not necessarily what it *actually* did.

**Why medium trust:** The agent has authorship but not editorial control. The append-only constraint prevents the agent from rewriting history to make its past decisions look better. However, the agent still authored the content — it can be incomplete, self-serving, or rationalized post-hoc.

### 1.4 Evidence Tier (Sessions)

The evidence tier contains independently captured records of what actually happened during agent execution.

**Author:** An independent harness — a component that captures LLM interactions before the agent can respond to them.

**Content:** Raw LLM calls and responses, timestamps, token counts, and any other execution metadata. In the reference implementation, this is `sessions/*.jsonl` captured by a harness implementing the [LLM Harness Protocol](https://github.com/ntholm86/principles-of-earned-autonomy/blob/main/docs/harness-protocol.md).

**Mutability:** Immutable after capture. Once a session record is written, it cannot be modified by any party.

**Trust semantics:** The evidence tier is independently verifiable. An observer can compare the agent's trace-tier account against the evidence-tier record to check for discrepancies.

**Why independent trust (not "lowest"):** The evidence tier has no authorship from the agent — the harness captures it before the agent sees the response. This is **capture-author separation**: the party being observed cannot author its own observation record. The trust is "independent" rather than "lowest" because it serves a different function than the other tiers — it exists to verify them, not to be verified by them.

### 1.5 Why Trust Level, Not Memory Type

Prior agent memory models ask: *What kind of information is this?*

- Episodic memory stores experiences and events
- Semantic memory stores facts and knowledge  
- Procedural memory stores skills and how-to knowledge
- Working memory holds active context for the current task

These categories are useful for retrieval and reasoning. An agent benefits from knowing whether it's recalling a past experience, a fact, or a procedure.

ACM asks a different question: *Who can I trust to have authored this?*

- Did the principal authorize this action? (Check intent tier)
- What does the agent claim it did? (Check trace tier)
- What actually happened? (Check evidence tier)

These categories are useful for governance. An observer benefits from knowing whether they're reading the principal's mandate, the agent's self-report, or an independent record.

The two organizations serve different purposes and can coexist. A conformant implementation might store episodic memories in the trace tier (agent-authored, append-only) while the evidence tier captures the raw interactions that generated those memories (harness-authored, immutable).

### 1.6 Conflict Resolution

When tiers disagree, resolve toward higher trust:

1. **Intent vs. Trace:** Intent wins. If the trace tier shows the agent interpreted the mandate one way, but the mandate says something else, the mandate is authoritative.

2. **Trace vs. Evidence:** Evidence wins. If the trace tier claims the agent did X, but the evidence tier shows it did Y, the evidence is authoritative.

3. **Intent vs. Evidence:** This is the interesting case. If the evidence shows the agent did something the mandate did not authorize, that is a governance failure — the agent acted outside its mandate. The mandate defines what *should* have happened; the evidence shows what *did* happen. Neither "wins" — the discrepancy is the finding.

---

## 2. Structural Requirements

Section 1 defined the three tiers. This section defines the structural properties that make the tier organization enforceable. These are not suggestions — they are conformance requirements. A system that violates them is not ACM-conformant.

### 2.1 Capture-Author Separation

**Definition:** The party being observed cannot author its own observation record.

In ACM terms: the agent cannot write to the evidence tier. The evidence tier is authored by an independent harness that captures LLM interactions before the agent receives the response.

**Why it matters:** If the agent could author its own evidence, the evidence tier would be no more trustworthy than the trace tier — both would be agent self-report. Capture-author separation is what gives the evidence tier independent standing.

This is the same structural property that makes audit logs trustworthy in security systems, flight recorders trustworthy in aviation, and financial ledgers trustworthy in accounting. The party with incentive to misrepresent cannot be the party that writes the record.

**Conformance criteria:**

A system is conformant if:
1. The evidence tier is written by a component architecturally separate from the agent
2. The agent cannot modify, delete, or suppress evidence-tier content
3. Evidence capture occurs before the agent receives LLM responses (not reconstructed after)

A system is non-conformant if:
- The agent writes its own session logs
- The agent can filter which interactions are captured
- Evidence is reconstructed from agent memory rather than captured independently

**Reference implementation:** The [LLM Harness Protocol](https://github.com/ntholm86/principles-of-earned-autonomy) implements capture-author separation by acting as a transparent proxy between the agent and the LLM API. The harness writes session records to `sessions/*.jsonl` before forwarding responses to the agent.

### 2.2 Append-Only Trace

**Definition:** The agent may add to its decision history but may not modify or delete existing entries.

In ACM terms: the trace tier (`audit-trail.md`, `retrospect.md`, etc.) grows monotonically. Corrections are made by appending new entries that reference and supersede earlier ones, not by editing the earlier entries.

**Why it matters:** If the agent could edit its past decisions, it could rationalize failures after the fact, making the trace tier unreliable for understanding what actually happened. Append-only constraint prevents history rewriting.

This is distinct from immutability. The trace tier *does* change — new entries are added. But it changes only by extension, never by revision. A reader can trust that entry N was written before entry N+1 and has not been modified since.

**Conformance criteria:**

A system is conformant if:
1. New trace entries are appended, never inserted or overwritten
2. Existing trace entries cannot be modified after commit
3. Corrections reference the entry being corrected (e.g., `[!REVERSAL]` markers)

A system is non-conformant if:
- The agent can edit past trace entries
- The agent can delete trace entries
- The system allows "silent" corrections that don't reference what was corrected

**Implementation note:** Git provides natural append-only semantics when used correctly. Each commit is a new append; the history is immutable once pushed. However, force-push and history rewriting violate append-only. A conformant implementation either disables force-push or treats rewritten history as a governance violation.

### 2.3 Trust-Tiered Conflict Resolution

**Definition:** When information in different tiers conflicts, resolve toward the tier with higher trust.

In ACM terms:
- Intent tier (principal-authored) overrides trace tier (agent-authored)
- Evidence tier (independently captured) overrides trace tier (agent-authored)
- Intent vs. evidence conflicts are governance findings, not resolution problems

**Why it matters:** Without a resolution rule, conflicting information creates ambiguity. The trust hierarchy provides a deterministic answer: when tiers disagree, the tier with less author-bias wins.

**Conformance criteria:**

A system is conformant if:
1. The system has a defined resolution order for tier conflicts
2. The resolution order favors lower author-bias (principal > independent > agent)
3. Intent-evidence conflicts are surfaced as findings rather than silently resolved

A system is non-conformant if:
- Trace-tier content can override intent-tier content
- Agent self-report is treated as authoritative when evidence contradicts it
- Conflicts between tiers are ignored or hidden

**Example:** The agent's trace tier claims it ran 5 iterations. The evidence tier shows 7 LLM calls. This is a discrepancy. Under trust-tiered resolution, the evidence (7 calls) is authoritative for what happened; the trace (5 iterations) may reflect how the agent *categorized* what happened, but it does not override the evidence.

### 2.4 Requirement Summary

| Requirement | What it constrains | Why it matters |
|-------------|-------------------|----------------|
| Capture-author separation | Agent cannot author evidence tier | Evidence is independently verifiable |
| Append-only trace | Agent cannot edit past decisions | History cannot be rewritten |
| Trust-tiered resolution | Conflicts resolve toward higher trust | Ambiguity has a deterministic answer |

These three requirements are interdependent:
- Capture-author separation makes the evidence tier trustworthy enough to override the trace tier
- Append-only trace ensures the trace tier's value (decision history) is preserved even though it's agent-authored
- Trust-tiered resolution defines how the tiers interact when they contain conflicting information

A system missing any one of these requirements has a governance gap that undermines the others.

---

## 3. The Mandate Gate

This is ACM's novel contribution. Prior agent memory models treat memory as a resource the agent uses during work. ACM treats memory as a precondition — a gate that must be passed before work begins.

### 3.1 Definition

**The Mandate Gate:** A pre-work mandate, authored by the principal, must exist in the agent's memory before any session of work is valid.

More precisely:
1. The intent tier must contain a mandate (governing context) before the session begins
2. The agent must read the mandate before taking any action
3. The agent's interpretation of the mandate must be visible in the trace tier

If any of these conditions is not met, the session is **unauthorized** — not merely suboptimal, but structurally invalid from a governance perspective.

### 3.2 What "Session Valid" Means

A session is **valid** if it operates under a mandate that:
- Existed before the session started
- Was authored by the principal (not the agent)
- Was read by the agent before any action
- Has a visible interpretation in the trace tier

A session is **invalid** if:
- No mandate exists (the agent acts without authorization)
- The mandate was created during the session (the agent authorizes itself)
- The mandate was not read (the agent ignores its authorization)
- No interpretation is visible (the agent's understanding cannot be checked)

Validity is binary. A session that begins without a mandate cannot become valid by creating one mid-session — the pre-work condition was already violated.

### 3.3 Why Pre-Work, Not During-Work

Prior memory models answer: *What does the agent need to remember to do the job?*

ACM answers: *What must be established before the agent is authorized to begin?*

This is a different question with different implications:

**Memory as resource (prior art):** The agent proceeds. Memory helps it proceed better. If memory is missing, the agent may perform suboptimally, but it still acts.

**Memory as precondition (ACM):** The agent cannot proceed until the mandate exists. Memory isn't just helpful — it's the authorization gate. If the mandate is missing, the agent must stop and request it, not act and hope.

The difference is structural, not a matter of degree. A capability-first model with excellent memory is still not governance-first if the agent can act without a mandate.

### 3.4 Interpretation Visibility

It is not enough for the mandate to exist and be read. The agent's *interpretation* of the mandate must be visible in the trace tier before action begins.

**Why this matters:** Two failure modes are possible even when a mandate exists:
1. The agent reads the mandate but misinterprets it
2. The agent reads the mandate but ignores parts of it

If the interpretation is invisible, these failures cannot be detected until after damage is done. Visible interpretation allows the principal (or an auditor) to catch misalignment *before* the agent acts on a wrong interpretation.

**What visible interpretation looks like:**
- The trace tier contains a statement of what the agent understood the mandate to mean
- This statement appears before any action entries
- The statement is specific enough that a reader can identify gaps between it and the mandate

**Example:** The mandate says "improve code quality without changing public APIs." The agent's trace entry shows: "Interpretation: refactor internal implementations, preserve all public method signatures and return types." A reader can verify alignment.

### 3.5 Historical Grounding

The mandate gate is not a novel invention — it is the application of an ancient governance principle to agent memory.

**Roman Law — Mandatum:** In Roman contract law, a *mandatum* was the formal authorization by which a principal empowered an agent to act on their behalf. The mandate:
- Was authored by the principal, not the agent
- Had to exist before the agent could act
- Defined the scope within which the agent was authorized
- Made the agent accountable for acting outside scope

The structure is identical to ACM's intent tier: principal-authored, pre-existing, scope-defining, accountability-enabling.

**Aviation — Pre-Flight Brief:** Before any flight, the crew conducts a brief that establishes the mission parameters. The brief:
- Must be completed before departure (pre-work gate)
- Covers what the mission is and what constraints apply
- Requires acknowledgment from all crew members
- Creates a shared understanding that can be referenced if things go wrong

**Surgery — Surgical Timeout:** Before incision, the surgical team conducts a timeout that verifies patient identity, procedure, and site. The timeout:
- Is a hard stop (no proceeding until complete)
- Ensures all participants have the same understanding
- Creates a documented checkpoint

All three share the same structure: a principal-authored or team-verified mandate must exist and be acknowledged before high-stakes action begins. The agent (pilot, surgeon, Roman agent) cannot proceed on assumption.

### 3.6 Conformance Criteria

A system is conformant if:
1. The intent tier contains a mandate before any session begins
2. The agent reads the intent tier as its first action in every session
3. The trace tier contains the agent's interpretation of the mandate before any other entries
4. Sessions that begin without a mandate are rejected or flagged as unauthorized

A system is non-conformant if:
- The agent can begin work without a mandate
- The agent can create its own mandate
- The agent can act before reading the mandate
- The agent's interpretation is not recorded before action

### 3.7 Relationship to PEA Principles

The mandate gate is ACM's operationalization of **PEA Principle 1: Commander's Intent**.

PEA Principle 1 states: *Define the destination, never the route. The agent must interpret and adapt rather than execute a checklist.*

The mandate gate implements this by:
- Requiring the principal to define the destination (intent tier)
- Requiring the agent to read and interpret it (visible interpretation)
- Allowing the agent to determine its own route (action entries in trace tier)

The principal says *what* and *why*. The agent determines *how*. But the agent cannot begin determining *how* until the *what* exists in memory.

---

## 4. Convergence

The mandate gate defines when work can *begin*. Convergence defines when work is *done*. Together they bound the governance arc: authorized start → governed execution → verified end.

### 4.1 Definition

**Convergence:** Work is done when the trace tier shows no remaining work items and independent evaluators examining the evidence tier find nothing left to change.

This is not the agent's assessment of its own work. The agent cannot declare itself done. Convergence is a structural property derivable from the memory state:
1. The work queue (in the trace tier) is empty
2. External review of the evidence tier produces no new findings

Both conditions must hold. An empty work queue with unreviewed evidence is not convergence — it's an untested claim. A reviewed evidence tier with remaining work items is not convergence — work remains.

### 4.2 Work Queue Empty

The trace tier contains the agent's work queue — what remains to be done. In the reference implementation, this appears in `retrospect.md` under "What the next runs should test."

**Work queue empty** means:
- No items remain in the "next runs" section
- No open questions or deferred work flagged in recent trail entries
- The most recent retrospect explicitly states the queue is exhausted

This is a necessary but not sufficient condition for convergence. The agent can exhaust its own list of ideas without having found everything worth finding.

### 4.3 Independent Evaluator Role

Convergence requires that independent evaluators — parties other than the agent — examine the evidence tier and find nothing left to change.

**Why independent:** The agent has an interest in being done. It produced the work. It will naturally see its work favorably. This is not dishonesty — it's structural bias. The same bias exists in human self-assessment and is why peer review exists.

**What independent evaluators do:**
1. Examine the evidence tier (session records)
2. Compare agent actions against the mandate
3. Check for gaps between what the mandate required and what the evidence shows
4. Identify findings the agent's trace tier did not surface

**Who counts as independent:**
- A different agent (model independence)
- The principal reviewing the evidence (role independence)
- An external auditor (organizational independence)

The agent itself does not count, regardless of how carefully it reviews its own work.

### 4.4 Why Agent Cannot Self-Assess

A core premise of ACM (inherited from PEA) is that the agent is an unreliable narrator of itself. This applies to completion assessment as much as to action narration.

**Self-assessment failure modes:**
1. **Premature convergence:** The agent declares done before finding everything findable
2. **Comfortable-corner bias:** The agent exhausts easy improvements and declares silence, missing harder ones
3. **Rationalized completion:** The agent produces a coherent narrative for why work is done that does not match the evidence

These are not accusations of agent dishonesty. They are structural properties of any self-assessing system with stakes in its assessment. The solution is the same as for narration: separation. The party doing the work is not the party declaring it done.

### 4.5 Silence as Signal

In ACM (and PEA), **silence** has a specific meaning: independent evaluators find nothing left to change.

Silence is:
- A signal, not a proof — it means no findings, not that no findings exist
- Bounded — silence applies to what was examined, not to what wasn't
- Provisional — new evidence or new evaluators may surface new findings

Silence is not:
- The agent saying "I'm done"
- The absence of agent activity
- A guarantee of correctness

**Bounded silence:** Every silence claim must name what quality bar it applies to and what surfaces were examined. "Silence on internal consistency for the trace tier" is bounded. "The work is done" is unbounded and therefore not a valid convergence claim.

### 4.6 Convergence at the Memory Level

Convergence is visible in the memory structure:

| Tier | Convergence Signal |
|------|-------------------|
| Intent | Mandate unchanged (work did not exceed scope) |
| Trace | Work queue empty, most recent entries show nothing actionable |
| Evidence | Independent review produced no new findings |

A memory state showing all three signals is converged. A memory state missing any signal is not.

**Example:** 
- Intent tier: `destination.md` unchanged since session start
- Trace tier: `retrospect.md` shows "Work queue: empty"; `audit-trail.md` most recent entry declares silence
- Evidence tier: Session records reviewed by independent evaluator; review notes show no findings

This memory state is converged. The convergence is derivable from the tiers — no declaration required.

### 4.7 Conformance Criteria

A system is conformant if:
1. Convergence is defined as a memory-state property, not an agent declaration
2. Work queue exhaustion is necessary but not sufficient for convergence
3. Independent evaluation is required before convergence can be claimed
4. Silence claims are bounded (name what was tested, what wasn't)

A system is non-conformant if:
- The agent can declare itself done
- Work queue empty alone constitutes convergence
- No independent review is required
- Silence claims are unbounded

### 4.8 Relationship to PEA Principles

Convergence is ACM's operationalization of **PEA Principle 3: Convergence Is Silence**.

PEA Principle 3 states: *The system has converged, for purposes of the loop, when diverse independent evaluators find nothing left to change.*

ACM implements this by:
- Requiring work queue exhaustion (trace tier signal)
- Requiring independent evaluation (external check of evidence tier)
- Treating silence as signal, not proof
- Requiring bounded silence claims

The agent does the work. The memory records it. Independent evaluators assess it. Convergence is what emerges when all three align on "nothing left to change."

---

## 5. Prior Art

ACM builds on prior work in agent memory architecture. This section identifies the main precedents, states what ACM inherits from each, and clarifies where ACM's contribution lies.

### 5.1 Cognitive Architectures for Language Agents (CoALA)

**Citation:** Sumers, T. R., Yao, S., Narasimhan, K., & Griffiths, T. L. (2023). *Cognitive Architectures for Language Agents*. arXiv:2309.02427.

**What it establishes:** CoALA provides a formal framework for language agent architecture organized around memory types: working memory (active context), episodic memory (past experiences), semantic memory (world knowledge), and procedural memory (action repertoire). The framework unifies prior agent designs under a cognitive-science-inspired taxonomy.

**What ACM inherits:**
- The recognition that agent memory requires explicit architectural treatment, not ad-hoc prompt engineering
- The multi-tier structure (though ACM organizes by trust, not function)
- The distinction between transient working memory and persistent long-term memory

**What ACM adds:**
- Trust-level organization orthogonal to functional types
- The mandate gate — a governance precondition absent from CoALA's capability-focused design
- Capture-author separation as a structural requirement
- Convergence as a memory-level property

CoALA asks: *What kinds of memory does an agent need?* ACM asks: *Who authored each memory tier, and can it be trusted?*

### 5.2 Generative Agents

**Citation:** Park, J. S., O'Brien, J. C., Cai, C. J., Morris, M. R., Liang, P., & Bernstein, M. S. (2023). *Generative Agents: Interactive Simulacra of Human Behavior*. arXiv:2304.03442.

**What it establishes:** Generative Agents introduced the observation → reflection → planning loop for persistent agents. Agents observe their environment, form reflections (higher-order summaries of observations), and use reflections to plan future action. Memory is retrieved by relevance, recency, and importance.

**What ACM inherits:**
- Reflection as a first-class memory operation (ACM's retrospect tier parallels this)
- The observation → reasoning → action cycle
- Importance-weighted memory retrieval as a design pattern

**What ACM adds:**
- Principal-authored intent tier (Generative Agents have no concept of externally-imposed mandate)
- Append-only trace constraint (Generative Agents can rewrite reflections)
- Independent evidence capture (Generative Agents have no harness-captured session records)
- Explicit governance layer (Generative Agents optimize for behavior plausibility, not accountability)

Generative Agents asks: *How can agents maintain coherent long-term behavior?* ACM asks: *How can principals verify that agent behavior was authorized?*

### 5.3 MemGPT

**Citation:** Packer, C., Wooders, S., Lin, K., Fang, V., Patil, S. G., Stoica, I., & Gonzalez, J. E. (2023). *MemGPT: Towards LLMs as Operating Systems*. arXiv:2310.08560.

**What it establishes:** MemGPT addresses the context window limitation by treating memory as a hierarchical system the agent actively manages. The agent promotes salient information to "main memory" (the context window) and demotes less relevant information to "archival memory" (persistent storage). The agent has explicit memory management functions.

**What ACM inherits:**
- Hierarchical memory with explicit tier boundaries
- Agent-managed memory promotion/demotion as a design pattern
- Cross-session persistence as a core requirement

**What ACM adds:**
- Trust differentiation between tiers (MemGPT tiers differ by salience, not by authorship or trust)
- The mandate gate (MemGPT has no pre-work authorization requirement)
- Capture-author separation (MemGPT's agent writes all tiers)
- Convergence as a structural property (MemGPT has no completion model)

MemGPT asks: *How can agents effectively use more memory than fits in context?* ACM asks: *How can observers trust what agents claim about their memory?*

### 5.4 The Governance Gap

The three prior works share a common assumption: memory exists to serve the agent's capabilities. They optimize for what the agent can do — retrieve relevant context, maintain coherent behavior, overcome context limits.

ACM starts from a different assumption: memory exists to serve the principal's governance needs. The first question is not "how can the agent use memory effectively?" but "how can the principal verify that the agent was authorized and that its account of what happened is trustworthy?"

This is the governance gap:

| System | Primary Concern | Mandate? | Evidence Capture? | Append-Only? |
|--------|-----------------|----------|-------------------|--------------|
| CoALA | Capability (what memory types does the agent need?) | No | No | No |
| Generative Agents | Behavior (how to maintain coherent action?) | No | No | No |
| MemGPT | Scale (how to use more memory than fits?) | No | No | No |
| **ACM** | **Governance (was the agent authorized? can we trust its account?)** | **Yes** | **Yes** | **Yes** |

ACM does not replace these systems. An ACM-conformant implementation may use CoALA's functional memory types internally, Generative Agents' reflection loop, or MemGPT's hierarchical memory management. The trust-tier organization is an additional layer that addresses questions the capability-focused systems do not ask.

### 5.5 Novel Claims

ACM claims novelty for:

1. **The Mandate Gate (Section 3)** — the requirement that a principal-authored mandate must exist in memory before any agent session is valid. No prior system requires pre-work authorization as a structural property.

2. **Capture-Author Separation (Section 2.1)** — the requirement that the evidence tier be captured by an independent harness, not authored by the agent. No prior system separates observation capture from agent authorship.

3. **Trust-Tier Organization (Section 1)** — the organization of memory by trust level (who authored it, whether it can be modified, how conflicts resolve) rather than by functional type. This is orthogonal to prior taxonomies.

4. **Convergence as Memory Property (Section 4)** — the definition of work completion as a structural property of the memory state, not an agent declaration. No prior system defines convergence at the memory level.

ACM does not claim novelty for tiered memory, reflection, or cross-session persistence. These are established contributions of prior work.

---

## 6. Reference Implementation

This section defines a minimal file-based implementation of ACM using a `.trail/` directory structure. This specification repository itself uses this structure and serves as a working example.

### 6.1 Directory Structure

An ACM-conformant repository contains a `.trail/` directory at the repository root with the following structure:

```
.trail/
├── destination.md      # Intent tier (principal-authored mandate)
├── audit-trail.md      # Trace tier (agent decisions, append-only)
├── retrospect.md       # Trace tier (derived orientation, rewritten each run)
└── sessions/           # Evidence tier (harness-captured session records)
    ├── 2026-06-20-001.jsonl
    └── ...
```

**Required files:**
- `destination.md` — the intent tier. Must exist before any agent session.
- `audit-trail.md` — the trace tier. Created by first agent run, append-only thereafter.

**Optional files:**
- `retrospect.md` — derived trace tier file. Contains current orientation and work queue. Rewritten (not appended) by retrospect operations.
- `sessions/*.jsonl` — evidence tier. Harness-captured LLM calls. Format and naming are implementation-dependent.

### 6.2 File Semantics by Tier

#### Intent Tier: `destination.md`

**Purpose:** The principal's mandate — what the agent is authorized to do, why, and what constraints apply.

**Author:** Principal only (confirmed content). The agent may assist in articulation but must never write to this file without explicit principal confirmation of the result.

**Content requirements:**
- What the work is (scope definition)
- What success looks like (completion criteria)
- What constraints apply (quality bar, style rules, forbidden actions)

**Mutability:** The principal may update or replace at any time. Changes should be appended or clearly marked; destructive rewrites lose the history of how the mandate evolved.

**Example structure:**
```markdown
# destination.md — [Project Name]

## What this work is
[Scope definition]

## What success looks like
[Completion criteria]

## Constraints
[Quality bar, rules, forbidden actions]
```

#### Trace Tier: `audit-trail.md`

**Purpose:** The agent's decision log — what was decided, why, and what happened.

**Author:** Agent only. The principal may read but should not edit.

**Content requirements:** Each entry must include:
- Date and entry identifier (slug)
- Interpretation of the ask
- Decision made (change, redesign argument, or silence)
- Action taken and verification evidence
- Reflection (model claim, blind spot, imagined reader pushback)

**Mutability:** Append-only. The agent may add entries but never modify or delete existing entries. This is the structural guarantee that makes the trace tier trustworthy.

**Format:** Markdown with entries separated by horizontal rules (`---`). Each entry is a dated block with a unique slug.

#### Trace Tier: `retrospect.md`

**Purpose:** Derived orientation — the current state of the work as understood by the loop.

**Author:** Agent (retrospect operation). The principal may read but should not edit.

**Content requirements:**
- Current claims about the target (what the arc shows is true)
- Work queue (what the next runs should test)
- Active operational rules (lessons learned that constrain future runs)

**Mutability:** Rewritten each retrospect run (not append-only). This file represents the *current* orientation, not the history. The history lives in `audit-trail.md`.

#### Evidence Tier: `sessions/*.jsonl`

**Purpose:** Independently captured session records — what actually happened at the LLM layer.

**Author:** Harness (independent capture mechanism). Neither agent nor principal should write to these files.

**Content requirements:** Implementation-dependent. At minimum:
- Timestamp
- Model identifier
- Prompt sent
- Response received
- Token counts

**Mutability:** Immutable after capture. Once a session record is written, it cannot be modified.

**Note:** The evidence tier is optional for minimal conformance but required for full ACM conformance. Without independent capture, the trace tier is the only record of what happened, and the agent authored it.

### 6.3 Minimal Conformance

A system is **minimally ACM-conformant** if:

1. **Intent tier exists before work begins.** A `destination.md` (or equivalent) must exist and be read by the agent before any session.

2. **Trace tier is append-only.** The agent's decision log cannot be modified after entries are written.

3. **Author separation is enforced.** The agent cannot write to the intent tier. The principal should not write to the trace tier.

4. **The mandate gate is implemented.** The system checks for intent-tier existence before authorizing agent action.

### 6.4 Full Conformance

A system is **fully ACM-conformant** if it meets minimal conformance plus:

1. **Evidence tier exists.** An independent harness captures LLM calls before the agent can respond.

2. **Capture-author separation is enforced.** The agent cannot write to the evidence tier.

3. **Convergence is structurally derivable.** The system can determine completion from the memory state (work queue empty + independent review) without relying on agent declaration.

4. **Trust-tiered conflict resolution is implemented.** When tiers disagree, the system resolves toward higher trust (Intent > Evidence > Trace).

### 6.5 This Repository as Reference

This specification repository (`agent-context-memory`) implements ACM:

- **Intent tier:** `.trail/destination.md` — authored by the operator, defines what the spec must achieve
- **Trace tier:** `.trail/audit-trail.md` — agent decisions for each spec section, append-only
- **Trace tier:** `.trail/retrospect.md` — current orientation and work queue
- **Evidence tier:** Not implemented in this repository (no harness capture)

This repository is therefore minimally conformant but not fully conformant. Full conformance would require adding an independent session capture harness.

The spec documents what the directory structure means. This repository demonstrates that the structure works.

---

## Appendix A: Relationship to PEA Principles

ACM operationalizes the three principles of [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy):

| PEA Principle | ACM Implementation |
|---------------|-------------------|
| **P1: Commander's Intent** | The Mandate Gate (Section 3) — work cannot begin without principal-authored intent |
| **P2: Observable Autonomy** | Capture-Author Separation (Section 2.1) + Append-Only Trace (Section 2.2) — agent reasoning is recorded and cannot be rewritten |
| **P3: Convergence Is Silence** | Convergence (Section 4) — completion is a memory-state property, not an agent declaration |

PEA is the theory. ACM is the implementation standard for the memory design problem.

---

## Appendix B: Glossary

**Agent:** An autonomous system that takes actions on behalf of a principal.

**Capture-author separation:** The structural requirement that the party recording evidence is not the party whose behavior is being recorded.

**Convergence:** The state where work is done — work queue empty and independent evaluators find nothing left to change.

**Evidence tier:** Memory captured by an independent harness, not authored by the agent.

**Harness:** An independent system that captures LLM calls and responses before the agent can respond.

**Intent tier:** Memory authored by the principal, defining what the agent is authorized to do.

**Mandate:** The principal's governing context — what, why, and what constraints apply.

**Mandate gate:** The requirement that a mandate must exist in memory before any agent session is valid.

**Principal:** The party with standing to authorize agent action (human operator, team, or organization).

**Silence:** Independent evaluators finding nothing left to change. A signal, not a proof.

**Trace tier:** Memory authored by the agent, recording its decisions and reasoning.

**Trust tier:** A layer of memory organized by trust level (who authored it, whether it can be modified, how conflicts resolve).

---

*End of specification.*
