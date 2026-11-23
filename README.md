# Shared Agent Context Memory (SACM)

**A specification for how AI-assisted work is kept organised in one shared memory, so that humans can audit it and stay responsible for the result.** Version 0.7.0 (a working draft). Published on GitHub and Zenodo under its earlier name, Agent Context Memory (ACM), until the rename is released.

📄 **[Read the full specification →](./SPEC.md)**

An agent starting a task begins from what earlier work learned, found and was corrected on, read from a memory kept apart from the code and organised by scope. One person is a team of one; only the number of people and agents writing changes.

## The mandate gate

Governed work needs a principal-confirmed mandate already in memory, read by the agent before it acts, with the agent's interpretation visible in the trace. The agent may help draft it but cannot confirm its own authority.

## Three tiers, by trust

| Tier | Role | Author |
|------|------|--------|
| **Intent** | The principal's mandate: what, why and constraints | Confirmed by the principal; the agent may draft |
| **Trace** | The agent's own account; source records are append-only | The agent |
| **Evidence** | Observations the agent cannot author, filter, alter or delete | A component separate from the agent |

21 numbered requirements (R-01 to R-21), each with a check in Appendix A, and two conformance levels: minimal (intent and trace; evidence may be absent, and says so) and full (adds independent capture). Every requirement is Proposed: nothing is claimed as implemented or conformant. The specification requires properties, not a layout; the reference layout is one folder per scope holding `mission.md`, `learning.md` and `records/`, in a memory repository of its own.

The [PEA skills suite](https://github.com/ntholm86/principles-of-earned-autonomy-skills-suite) and the [Work skill](https://github.com/ntholm86/work-skill) implement it. The specification names no implementation; another framework can implement it. It implements the [Principles of Earned Autonomy](https://github.com/ntholm86/principles-of-earned-autonomy), its one dependency.

## Citation

Cite SACM using [CITATION.cff](./CITATION.cff). Version 0.7.0 has no archive record yet; 0.5.0 is [DOI 10.5281/zenodo.24431974](https://doi.org/10.5281/zenodo.24431974); the concept DOI [10.5281/zenodo.20785505](https://doi.org/10.5281/zenodo.20785505) covers all versions.

[CC BY-SA 4.0](./LICENSE) — [Nils Wendelboe Holmager](https://nilsholmager.com/), 2026
