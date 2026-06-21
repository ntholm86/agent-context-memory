# retrospect.md -- Agent Context Memory (ACM)

_Last updated: 2026-06-21 (run: scoped-memory-retro-refresh)_

## Current claims

1. **The spec now has four complete sections plus Convergence as section 5.** Section 4 (Scoped Memory) was added 2026-06-21. The spec defines two orthogonal governance axes: trust (Intent > Trace > Evidence) and scope (higher scope > lower scope). This is the most structurally significant addition since the initial draft.

2. **The naming decision is settled.** ACM (Agent Context Memory) with the mandate gate as the core contribution inside the spec. The umbrella/principle pattern matches PEA.

3. **The mandate gate is in effect for this repo.** destination.md existed and was read before any spec work began. The repo self-demonstrates the pattern it specifies.

4. **Prior art is formally cited.** Section 1 contains verified arXiv citations for CoALA (2309.02427), Generative Agents (2304.03442), and MemGPT (2310.08560). The governance gap is explicitly positioned.

5. **Novel claims are bounded.** The spec claims novelty for: mandate gate, capture-author separation, trust-tier organization, convergence as memory property, and scope hierarchy as a core governance axis. It does not overclaim tiered memory, reflection, or persistence.

6. **The "work queue EMPTY" claim from 2026-06-20 is superseded.** Section 4 (Scoped Memory) was not in scope when that retro was written. The queue is no longer empty.

---

## What the next runs should test

1. **Stop-condition definition for scope traversal** -- Section 4.2 describes "natural boundary" without defining one. Options: filesystem root, absence of .acm/ for two consecutive levels, explicit marker file (.acm-root). An ambiguous spec is a conformance risk.

2. **Conformance test suite for Section 4** -- automated tests that verify an implementation discovers parent scopes, reads higher-scope mandates, and resolves conflicts in favor of higher scope.

3. **External review** -- have independent readers assess the spec for clarity, defensibility, gaps in Section 4 specifically.

4. **Evidence tier harness** -- implement session capture to achieve full conformance in this repo.

5. **Academic submission** -- prepare the spec for formal publication.

---

## Active operational rules

1. **Append-only for audit-trail.md.** This rule applies to this repo as it does to all PEA-conformant repos.

2. **Never write destination.md from agent runs.** The destination is operator-held. Retrospect reads it but never modifies it.

3. **Prior art must be cited honestly.** The mandate gate and scope hierarchy are the novel claims. Tiered memory, reflection, persistence are prior art. Do not overclaim.

4. **The repo demonstrates what it specifies.** ACM's .acm/ is the reference implementation of ACM. If the spec says something, this repo must do it.

5. **Higher-scope mandates must be read before this repo's own mandate.** Per ACM Section 4, traverse parent directories for .acm/destination.md before reading this repo's. The workspace destination governs this repo's work.

---

## Loop-effectiveness notes

Prior retro declared work queue empty after completing the spec. Section 4 (Scoped Memory) was added 2026-06-21 based on workspace-level design decision (destination-run-workspace-v1 trail entry). The queue is now active again. Next phase requires external review the loop cannot provide unilaterally.