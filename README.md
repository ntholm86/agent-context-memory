# Agent Context Memory (ACM)

**A specification for governance-first context memory in autonomous AI agent systems.**

📄 **[Read the full specification →](./SPEC.md)**

If an agent begins work without a principal-authored mandate already in memory, the session is unauthorized. That is the mandate gate. It is what makes context memory *governance-first* rather than merely *persistent*.

---

## What this is

ACM is a specification for how autonomous AI agents should organize, protect, and use their context memory. It covers the full scope of agent memory — what prior work already established — and adds a governance layer no prior specification defines.

ACM is the operationalization of [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy) for the context memory design problem. PEA is the theory; ACM is the implementation standard for memory.

---

## The novel claim

**Principle 1 — The Mandate Gate:** A pre-work mandate must exist in memory, authored by the principal, before any agent session of work is valid. The agent must read it before touching anything else.

Every prior context memory model (MemGPT, CoALA, Generative Agents) treats memory as a working resource the agent draws from *during* work. ACM treats memory as a *precondition* — the gate that determines whether work can begin at all. Without the mandate, the session is not authorized.

---

## How it works

ACM defines a three-tier memory structure organized by **trust level**, not memory type:

| Tier | Role | Author | Files |
|------|------|--------|-------|
| **Intent** (highest trust) | Principal's mandate — governs all sessions | Principal only | `destination.md` |
| **Trace** (medium trust) | Agent's decision history — append-only | Agent | `audit-trail.md`, `retrospect.md` |
| **Evidence** (independent) | Captured LLM interactions — agent-inaccessible | Harness | `sessions/*.jsonl` |

**Structural requirements:**

- **Mandate gate** — intent tier must exist and be read before session is valid
- **Capture-author separation** — evidence tier cannot be authored by the agent
- **Append-only trace** — the agent extends but never rewrites its decision history
- **Trust-tiered conflict resolution** — intent > trace > evidence when tiers disagree

**Convergence at the memory level:** work is done when the trace tier shows an empty work queue and independent evaluators find nothing left to change.

---

## Relationship to prior art

ACM is governance-first. Prior work is capability-first:

- **CoALA** (Sumers et al., 2023) — episodic, semantic, procedural, working memory tiers. ACM inherits tiered structure, adds trust-based differentiation.
- **Generative Agents** (Park et al., 2023) — observation → reflection → planning. ACM inherits reflection as a memory operation (retrospect), adds the mandate gate.
- **MemGPT** (Packer et al., 2023) — hierarchical memory with cross-session persistence. ACM inherits persistence, adds capture-author separation.

These models optimize for what the agent *can do*. ACM optimizes for whether the operator *can trust* what the agent did, and whether the agent was *authorized* to begin at all.

---

## Reference implementation

The `.trail/` directory pattern is the reference implementation of ACM:

```
.trail/
├── destination.md        # Intent tier — mandate (principal-authored)
├── audit-trail.md        # Trace tier — append-only decision log
├── retrospect.md         # Trace tier — current orientation (rewritten by retrospect)
└── sessions/             # Evidence tier — harness-captured JSONL
    └── *.jsonl
```

The mandate gate is in force when `destination.md` exists before any session begins.

---

## Citation

If you use ACM, please cite it using the metadata in [CITATION.cff](./CITATION.cff).

---

## License

[CC BY-SA 4.0](./LICENSE) — Nils Wendelboe Holmager, 2026
