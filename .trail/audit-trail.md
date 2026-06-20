# audit-trail.md — Mandated Context Memory

Append-only decision log. Each entry records one iteration of work.

---

## 2026-06-20 — repo-initialization

**Ask:** Initialize the MCM repo with `.trail/` structure and capture destination before starting work.

**Examination:** Empty repo with `.git/` and empty `.trail/`. No prior work exists.

**Decision:** Create `destination.md` capturing the full scope: what MCM is, the novel claim (mandate gate), relationship to prior art (CoALA, MemGPT, Generative Agents), what the spec defines, and the quality bar.

**Action:** Created `.trail/destination.md` and `.trail/audit-trail.md`.

**Reflection:** The destination is now established. The mandate gate is in effect for this repo — the principal's intent exists before any spec work begins. Next: scaffold the spec itself.
