# Agent Context Memory (ACM)

**A specification for governance-first context memory in autonomous AI agent systems.**

ðŸ“„ **[Read the full specification â†’](./SPEC.md)**

If an agent begins work without a principal-authored mandate already in memory, the session is unauthorized. That is the mandate gate. It is what makes context memory *governance-first* rather than merely *persistent*.

---

## What this is

ACM is a specification for how autonomous AI agents should organize, protect, and use their context memory. It recognizes that agent memory is fundamentally an authorization problem and applies established access control patterns â€” the "authorization before action" principle common to RBAC, MAC, and capability-based security â€” to this domain.

ACM is the operationalization of [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy) for the context memory design problem. PEA is the theory; ACM is the implementation standard for memory.

---

## The core contribution

**Principle 1 â€” The Mandate Gate:** A pre-work mandate must exist in memory, authored by the principal, before any agent session of work is valid. The agent must read it before touching anything else.

This is the "authorization before action" principle â€” familiar from RBAC, MAC, and capability-based security â€” applied to agent sessions. Every prior context memory model (MemGPT, CoALA, Generative Agents) treats memory as a working resource the agent draws from *during* work. ACM treats memory as a *precondition* â€” the gate that determines whether work can begin at all. Without the mandate, the session is not authorized.

---

## How it works

ACM defines a three-tier memory structure organized by **trust level**, not memory type:

| Tier | Role | Author | Files |
|------|------|--------|-------|
| **Intent** (highest trust) | Principal's mandate â€” governs all sessions | Principal only | `destination.md` |
| **Trace** (medium trust) | Agent's decision history â€” append-only | Agent | `audit-trail.md`, `retrospect.md` |
| **Evidence** (independent) | Captured LLM interactions â€” agent-inaccessible | Harness | `sessions/*.jsonl` |

**Structural requirements:**

- **Mandate gate** â€” intent tier must exist and be read before session is valid
- **Capture-author separation** â€” evidence tier cannot be authored by the agent
- **Append-only trace** â€” the agent extends but never rewrites its decision history
- **Trust-tiered conflict resolution** â€” intent > trace > evidence when tiers disagree
- **Scope hierarchy** — ACM memory can exist at nested scopes (repo → workspace → org). Higher-scope mandates govern lower-scope ones. Scopes are discovered by parent-directory traversal, stopping at a `.acm-root` ceiling marker or the filesystem root. See §4 Scoped Memory.

**Convergence at the memory level:** work is done when the trace tier shows an empty work queue and independent evaluators find nothing left to change.

---

## Relationship to prior art

ACM applies established access control patterns to agent memory:

- **RBAC** â€” authorization before action, scope defined by authorizer, actor accountable for exceeding scope. ACM applies this at the session level (mandate gate).
- **CoALA** (Sumers et al., 2023) â€” episodic, semantic, procedural, working memory tiers. ACM inherits tiered structure, adds trust-based differentiation.
- **Generative Agents** (Park et al., 2023) â€” observation â†’ reflection â†’ planning. ACM inherits reflection as a memory operation (retrospect), applies audit-log principles.
- **MemGPT** (Packer et al., 2023) â€” hierarchical memory with cross-session persistence. ACM inherits persistence, adds capture-author separation.

These models optimize for what the agent *can do*. ACM optimizes for whether the operator *can trust* what the agent did, and whether the agent was *authorized* to begin at all.

---

## Relationship to Augmented Individual Intelligence

[Augmented Individual Intelligence (AII)](https://doi.org/10.5281/zenodo.18417872) defines five boundary conditions for when human-AI coupling qualifies as cognitive partnership. The fifth condition is **agency preservation**: the human retains final decision authority and can override AI suggestions.

ACM provides the enforcement mechanism for this requirement. The mandate gate ensures the principal authors the authorization; capture-author separation ensures the agent cannot rewrite its own observation record; append-only trace ensures the agent cannot rationalize overstepping after the fact.

**ACM is infrastructure for AII-qualifying practices.** Without governance-first memory, agency preservation is behavioral hope. With ACM, it is structural guarantee.

---

## Reference implementation

The `.acm/` directory pattern is the reference implementation of ACM:

```
.acm/
â”œâ”€â”€ destination.md        # Intent tier â€” mandate (principal-authored)
â”œâ”€â”€ audit-trail.md        # Trace tier â€” append-only decision log
â”œâ”€â”€ retrospect.md         # Trace tier â€” current orientation (rewritten by retrospect)
â””â”€â”€ sessions/             # Evidence tier â€” harness-captured JSONL
    â””â”€â”€ *.jsonl
```

The mandate gate is in force when `destination.md` exists before any session begins.

---

## Citation

If you use ACM, please cite it using the metadata in [CITATION.cff](./CITATION.cff).

---

## License

[CC BY-SA 4.0](./LICENSE) â€” Nils Wendelboe Holmager, 2026

