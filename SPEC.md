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

**Author:** The principal (human operator, team, or organization). Never the agent.

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

*[Sections 3-6 to follow in subsequent iterations]*
