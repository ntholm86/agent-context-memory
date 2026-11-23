# Shared Agent Context Memory (SACM)

**A specification for how AI-assisted work is kept organised in one shared memory, so that humans can audit it and stay responsible for the result.** Version 0.7.0 (a working draft). Published on GitHub and Zenodo under its earlier name, Agent Context Memory (ACM), until the rename is released.

📄 **[Read the full specification →](./SPEC.md)**

An agent starting a task begins from what earlier agent work learned, found and was corrected on, read from a memory kept apart from the code and organised by scope. The same model serves one person and a whole organisation: a person working alone is a team of one, and only the number of people and agents reading and writing changes.

What keeps it auditable is the **mandate gate**: governed work needs a principal-confirmed mandate already in memory; without one, a session is not a SACM-governed interval. It is what makes context memory *governance-first* rather than merely *persistent*.

---

## What this is

SACM is a specification for how autonomous AI agents should organize, protect, and use their context memory, so that work accumulates across people, sessions, tools and models while the principal keeps authority. It applies established access control and audit-log patterns — "authorization before action", as in RBAC, MAC and capability-based security — to agent memory.

SACM implements the [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy) for the context memory design problem: PEA is the theory, and its principles are SACM's one dependency. SACM stands on its own; it can be implemented without the PEA skills.

---

## What it is for

Intended outcomes, not yet demonstrated results: learning that accumulates across people (a correction made once becomes everyone's starting point); continuity across sessions, tools and models; earned autonomy at team scale (a shared, auditable record is what lets a team decide how much to delegate, and an agent that works from the team's accumulated context is safer to delegate to); orientation for a newcomer, human or agent; attributable records that can serve as audit evidence; and memory the principal can own, tied to no tool or vendor.

---

## The main proposal: the mandate gate

A pre-work mandate, confirmed by the principal, must exist in memory before work is a governed interval: a confirmed mission or, where there is none, the interpretation of the prompt that the principal confirmed for the run, recorded at its checkpoint. The agent may help draft it but cannot confirm its own authority. The three context memory models SACM compares itself with (MemGPT, CoALA, Generative Agents) treat memory as a working resource the agent draws from *during* work. SACM treats memory as a *precondition*: the gate that determines whether SACM-governed work can begin.

---

## How it works

Memory is organized by **trust level**, not memory type:

| Tier | Role | Author |
|------|------|--------|
| **Intent** (authorization) | The principal's mandate: what, why and constraints. Governs SACM-governed work | Confirmed by the principal; the agent may draft |
| **Trace** (self-report) | The agent's decisions, interpretations and outcomes. Source records are append-only | The agent |
| **Evidence** (independent observation) | Execution observations the agent cannot author, filter, alter or delete | A component separate from the agent |

Version 0.7.0 specifies 23 numbered requirements (R-01 to R-23), each with a check in Appendix A. Every one is Proposed: nothing is claimed as implemented or conformant. The specification's Appendix C lists what each version changed.

---

## Reference layout

The specification requires properties, not a layout. The reference layout is:

```
mission.md, learning.md, records/        the memory-wide scope
repositories/<id>/repository.json            the binding for one repository of work
repositories/<id>/                           that repository's scope (the same three entries)
repositories/<id>/paths/<path>/              a path scope
tasks/<task>/                                a task scope (under the memory, a repository or a path)
```

**Known implementations.** The [PEA skills suite](https://github.com/ntholm86/principles-of-earned-autonomy-skills-suite) and the [Work skill](https://github.com/ntholm86/work-skill) implement this specification: they resolve a bound scope in a separate memory repository and write its records. The specification names no implementation and neither is required; another framework can implement it.

---

## Relationship to prior art

- **RBAC** — authorization before action, scope defined by authorizer, actor accountable for exceeding scope. SACM applies this at the session level.
- **CoALA** (Sumers et al., 2023) — tiered memory. SACM builds on the tiers and adds trust-based differentiation.
- **Generative Agents** (Park et al., 2023) — observation, reflection, planning. SACM builds on reflection as a memory operation and applies audit-log principles.
- **MemGPT** (Packer et al., 2023) — hierarchical memory with cross-session persistence. SACM builds on persistence and adds capture-author separation.

These models optimize for what the agent *can do*. SACM optimizes for whether the principal *can trust* what the agent did, and whether the agent was *authorized* to begin at all.

---

## Relationship to Augmented Individual Intelligence

[Augmented Individual Intelligence (AII)](https://doi.org/10.5281/zenodo.18417872) names agency preservation as a boundary condition for human-AI cognitive partnership: the human retains final decision authority. SACM states the memory structure such a condition would need: the principal authors the mandate, the agent cannot rewrite its own observation record, and source records are append-only. It does not claim an implementation that enforces this.

---

## Citation and archive

Cite SACM using [CITATION.cff](./CITATION.cff). Version 0.7.0 has no archive record yet; version 0.5.0 is archived as [DOI 10.5281/zenodo.24431974](https://doi.org/10.5281/zenodo.24431974); the concept DOI [10.5281/zenodo.20785505](https://doi.org/10.5281/zenodo.20785505) covers all versions (earlier: v0.3.2, 10.5281/zenodo.20785506).

## License

[CC BY-SA 4.0](./LICENSE) — Nils Wendelboe Holmager, 2026

---

Author: [Nils Wendelboe Holmager](https://nilsholmager.com/) · [Project context and connected work](https://nilsholmager.com/research/agent-context-memory/)
