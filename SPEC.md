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

*[Sections 2-6 to follow in subsequent iterations]*
