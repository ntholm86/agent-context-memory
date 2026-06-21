# destination.md — Agent Context Memory (ACM)

_Written by operator: Nils Wendelboe Holmager | June 20, 2026_

---

## What this work is

**Agent Context Memory (ACM)** is a specification for context memory in autonomous AI agent systems. It covers the full scope of agent memory (all purposes prior work already serves) and adds a governance layer that no prior specification defines.

ACM is the operationalization of Principles of Earned Autonomy (PEA) for the context memory design problem. PEA is the theory; ACM is the implementation standard for memory.

---

## The novel claim

**Principle 1 — The Mandate Gate:** A pre-work mandate must exist before the agent is authorized to act. The principal (operator/team) must have authored the governing context, and the agent must have read it, before any session of work is valid.

This is what makes ACM governance-first rather than merely capability-first. Every prior memory model (MemGPT, CoALA, Generative Agents) treats memory as a working resource the agent draws from *during* work. ACM treats memory as a precondition — the gate that determines whether work can begin at all.

---

## Relationship to prior art

ACM does not ignore or contradict prior work. It builds on it:

- **CoALA (Sumers et al., 2023)** — formally modeled agent memory as four typed tiers: working, episodic, semantic, procedural. ACM adopts the tiered structure, adds trust-based tier differentiation.
- **Generative Agents (Park et al., 2023)** — observation → reflection → planning. ACM adopts reflection as a memory operation (retrospect), adds the mandate gate.
- **MemGPT (Packer et al., 2023)** — hierarchical memory with persistence across sessions, promotion/demotion. ACM adopts cross-session persistence, adds capture-author separation.

These are capability-first memory models: they optimize for what the agent can do. ACM is governance-first: it optimizes for whether the operator can trust what the agent did, and whether the agent was authorized to begin at all.

---

## What the spec defines

The ACM spec must define:

### 1. The three tiers (by trust level, not memory type)

| Tier | Name | Trust level | Author | Example files |
|------|------|-------------|--------|---------------|
| Intent | Mandate | Highest | Principal only | `destination.md` |
| Trace | Decisions | Medium | Agent (append-only) | `audit-trail.md`, `retrospect.md` |
| Evidence | Sessions | Lowest | Harness (independent) | `sessions/*.jsonl` |

### 2. Structural requirements

- **Capture-author separation** — the evidence tier cannot be authored by the agent. An independent harness captures LLM calls before the agent can respond.
- **Append-only trace tier** — the agent can extend but never rewrite its decision history.
- **Trust-tiered organization** — intent tier is authoritative, trace is agent-derived, evidence is independently captured. Conflicts resolve toward higher trust.

### 3. The mandate gate

The session is not valid unless:
- The intent tier exists (destination.md or equivalent)
- The agent has read the intent tier before acting
- The agent's interpretation of the mandate is visible in the trace tier

### 4. Convergence at the memory level

The work is done when:
- The trace tier shows no open work items (retrospect → empty queue)
- Independent evaluators examining the evidence tier find nothing left to change

---

## What success looks like

A published ACM v1.0 spec that:
- Can be adopted independently of PEA (but cites PEA as origin theory)
- References CoALA/MemGPT/Generative Agents as capability-first prior art
- Claims the mandate gate and governance-first architecture as novel
- Defines a directory structure (`.trail/`) implementable today
- Is defensible against the literature scan already performed

---

## Quality bar

The spec must be:
- **Scientifically honest** — acknowledges prior art, claims only the novel contribution
- **Practically adoptable** — an organization can implement it from the spec alone
- **PEA-aligned** — implements all three PEA principles in the memory layer
- **Citable** — has CITATION.cff, versioned, persistent home
