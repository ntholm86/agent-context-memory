# Shared Agent Context Memory (SACM) Specification

**Version:** 0.7.0
**Date:** 2026-11-22
**Author:** Nils Wendelboe Holmager
**License:** CC BY-SA 4.0
**Status:** Working draft; not an implementation-conformance claim. Every requirement is Proposed: none has been exercised by anyone but its author.
**Baseline:** ACM v0.4.0 (the name this specification had before SACM; commit 51e951e of this repository; never tagged).

## Abstract

Shared Agent Context Memory (SACM) specifies governance-first context memory for autonomous AI agents. Its purpose is cumulative,
trustworthy work across people, sessions, tools and models, while the principal retains authority. Memory supplies the
governed starting point for the next contributor rather than requiring the work to start over.

SACM transfers established authorization and audit-log patterns to agent memory governance. Its mandate gate requires
principal-confirmed intent before governed work. Three trust tiers distinguish authorization, agent self-report and
independently captured evidence. Scope identifies what the memory governs; access controls identify who can use it.

The same model serves one contributor and many. Memory resides in its own repository, separately from repositories of work.
Binding connects work to that memory; scoped retrieval makes it usable without reading the entire accumulated history.
The specification defines required properties, not a mandatory vendor, configuration format, storage engine or skill host.

SACM operationalizes [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy).
It can be implemented independently of that reference ecosystem.

## Reading

MUST, MUST NOT, SHOULD and MAY express conformance requirements; each has a stable identifier and a check in Appendix A.
Every requirement is Proposed. The checks are specified tests, not executed results.

Terms: the *mandate* is the intent tier (`mission.md`; older memories call it `destination.md`); *records* and the derived
`learning.md` are the trace tier (*trail* means the same, and `audit-trail.md` is the legacy single-file form); `evidence/` is
the evidence tier.

## 1. Trust Tiers

| Tier | Responsibility | Authority |
|------|----------------|-----------|
| Intent | Principal-confirmed mandate: what, why, constraints | Governs authorization; changes need principal confirmation |
| Trace | Attributable agent decisions, interpretations, corrections and outcomes | Source is append-only; derived views are replaceable |
| Evidence | Independently captured execution observations | The agent cannot author, filter, alter or delete the capture |

**R-01.** An implementation MUST identify each artifact's tier and whether it is source or derivation. A summary, index or learning file gains no evidentiary standing by being stored beside source records.

**R-02.** Each governing scope MUST identify its principal and the mechanism by which that principal confirms a mandate or
delegates authority. A principal may be a person, role or collective with an explicit decision rule. Agent-drafted candidate
text MUST NOT become intent without that confirmation. The principal owns the meaning, not necessarily all the typing.
Confirmation and mandate revisions MUST be identifiable to later readers. An agent may assist in articulation; it cannot
approve its own authority, infer approval from silence, or silently replace a mandate. Changes made by the principal do not
retroactively authorize earlier actions.

**R-03.** Intent MUST determine what was authorized; independently captured evidence MUST determine what was observed within its coverage; trace MUST remain agent self-report and MUST NOT override either. Evidence contradicting intent is a governance finding, not a reason to rewrite either; capture gaps are recorded. A recalled decision is not automatically a new instruction.

## 2. Structural Requirements

**R-04.** Where an evidence tier is claimed, a component architecturally separate from the observed agent MUST capture the
declared interactions before releasing their responses to that agent. The agent MUST NOT be able to suppress or edit that
capture. Coverage, gaps and capture failures MUST be observable. Reconstructed self-report is not independent evidence. For
response streaming, capture must precede delivery of each captured portion. A full-evidence claim MUST identify any
unobserved tools, channels or operations rather than implying comprehensive execution coverage.

**R-05.** Accepted trace source records MUST be immutable to ordinary contributors: addition is permitted, replacement and
deletion are not. A correction MUST create a new record referring to the corrected identifier. Derived views MAY be
replaced, but MUST identify their source and MUST NOT be the only surviving copy of a decision. Git history and an instruction
saying "do not edit" are not alone enforcement: an implementation claiming structural protection must reject such changes at
its acceptance boundary. Record any administrative override as a governance event.

**R-06.** An implementation MUST enforce the separation of mandate confirmation, trace addition and evidence capture at its
acceptance boundary. Rejected or unavailable operations MUST produce an explicit failure, not success-shaped output.
If protection depends on instructions rather than enforcement, disclose that limitation and do not claim structural conformance.

**R-07.** The recording, access and retention policy MUST be established before recording begins, for a solo contributor as well as many. Scope relevance MUST NOT substitute for permission, and a sub-repository or submodule does not by itself stop readers of another scope from obtaining its content. Secret detection can supplement the policy but proves nothing about natural-language records.

**R-08.** An implementation MUST expose any conflict between append-only preservation and its principal's retention or erasure obligations before claiming both are met. An exceptional, separately authorized erasure procedure MUST record the affected identifiers, authority, reason, affected replicas and verification limits without repeating erased content, and MUST distinguish an intentionally missing record from a silently lost one. The adopter determines lawful retention; SACM gives no legal opinion and no guarantee that every backup, cache or clone can be recalled.

## 3. The Mandate Gate

**R-09.** Before an SACM-governed work interval begins, all applicable principal-confirmed mandates MUST exist, be resolved
and be read. The agent's interpretation, scope, mandate revisions and authority to proceed MUST be visible in accepted trace
before the first change or other consequential action. Examination that changes nothing may precede that record, and its
findings are recorded afterward. Binding validation and mandate reading are preflight, not authorization to alter the work.
The governed interval starts only after the gate passes: earlier setup or articulation does not retroactively validate
unauthorized work, and an agent cannot authorize itself by creating a mandate mid-work.

**R-10.** Higher-scope mandates MUST govern lower scopes; lower scopes MAY narrow authority, never expand it. Unresolved peer conflicts MUST be surfaced to the designated authority; neither the newest record nor the agent's preference wins. If applicable authority changes during work, affected actions MUST pause until the governing context and interpretation are revalidated. A departed owner or inaccessible authority confers no standing on the next writer; the missing authorization is surfaced.

**R-11.** Missing, inaccessible, invalid or ambiguous binding, governing mandate or required trace acceptance MUST be reported explicitly, and dependent work MUST pause. Work independently authorized by the current prompt and available governing context MAY continue but MUST NOT be represented as a valid SACM-governed interval without passing R-09. The implementation MUST NOT claim unavailable memory was read, invent a replacement mandate, silently switch memory sources or silently create a competing local trail; records may be queued only if policy permits and are not represented as accepted shared records.

## 4. Memory Repository and Scopes

**R-12.** Memory MUST reside in a repository of its own, separate from the repositories of work (an identified, governed store with its own acceptance and access boundary, not necessarily Git). Each repository of work MUST have a repository-level scope in it; one memory MAY hold many. Adding contributors MUST NOT require a different storage model or a migration. A distribution of a work repository MUST NOT contain its development memory.

**R-13.** Setup MUST establish an explicit binding between the repository of work, the memory source and the repository-level scope, once per deployment rather than in prompts, and the implementation MUST validate it before memory use. Folder names and remote basenames alone MUST NOT establish identity; relocating or renaming a clone MUST NOT silently change the binding; ambiguous, conflicting or mismatched bindings MUST fail visibly, with no guessed read or write. A remote URL is a locator, not identity, and a rename needs an explicit migration of the binding. Where the mechanism is the root commit, it identifies shared history, not governance: clones and forks sharing a root commit share one binding, and governing a fork separately needs another confirmed discriminator.

**R-14.** Within the validated binding, repository-relative code locations MUST resolve deterministically to their path scopes; named feature, change-request or task scopes MUST be selected from established task intent, not guessed from a path; ambiguity MUST be surfaced before writing. A record goes to the narrowest applicable scope: the task scope the established task names, else the path scope containing the work, else the repository scope, else (work not tied to a repository) the memory-wide scope. Work spanning scopes is recorded in the authorized coordinating scope or through linked records, with no unexplained duplication of a decision. Moves of scopes or code paths need an explicit old-to-new mapping, and existing references MUST remain resolvable.

**R-15.** Resolution MUST return every applicable governing scope, broadest first, with the active write scope. It MUST stop at the principal-declared memory ceiling, apply path containment and the declared access boundaries, report any it could not apply, and not leave the binding through parent segments, absolute paths, symbolic links or equivalent redirects. If a safety bound prevents complete resolution it MUST report failure, not drop governing scopes silently. A required but inaccessible higher mandate blocks the affected work; lower mandates alone do not justify proceeding.

**R-16.** Each source record MUST identify its scope (or explicit memory-wide context), covered work-repository identities, actor, principal or authority reference, applicable mandate revisions, work or session identifier, record identifier, time and relevant verification references. Agent or model identity MUST be stated where known, otherwise "unknown", never invented. Asserted identity is recorded separately from authenticated identity (a Git author field is not authentication). A preflight observation made before work identity or revisions are established marks those fields unresolved, which grants no authority; later association is recorded without rewriting the original. Evidence belongs to the scope of the documented actions: a cross-scope session has one canonical capture with scope references, not copies that imply separate observations.

## 5. Convergence

**R-17.** Convergence MUST require both an exhausted work queue for the declared scope and independent evaluation finding
nothing further to change against its declared quality bar. The agent's self-assessment or a passing automated check alone
MUST NOT establish convergence. An evaluation identifies the artifact revision, examined and untested surfaces, evaluator
identity and independence, and whether it is fresh or a same-session follow-up. A different model family reduces one source of
shared bias; it is not proof of statistical independence. Silence is bounded, provisional evidence of no findings, not proof
that none exist; deferred items outside an evaluated scope remain visible.

## 6. Prior Art and Contribution

SACM builds on three capability-first memory models: Sumers et al. (2023), *Cognitive Architectures for Language Agents* ([arXiv:2309.02427](https://arxiv.org/abs/2309.02427)), which distinguishes functional memory types; Park et al. (2023), *Generative Agents* ([arXiv:2304.03442](https://arxiv.org/abs/2304.03442)), the observation, reflection and planning precedent; and Packer et al. (2023), *MemGPT* ([arXiv:2310.08560](https://arxiv.org/abs/2310.08560)), hierarchical memory and cross-session persistence. It keeps those capabilities and adds governance: authorship, authority and verifiability, with reflection as a derivation distinct from source evidence and confirmed intent. Role-based access control (Sandhu et al., 1996), MAC and capability models establish authorization before action and separation of actor from authority; independent capture and append-only source apply established audit-log patterns. These analogies motivate SACM; they do not prove that natural-language mandates are machine-enforceable.

Its stated contribution is transferring established authorization patterns to agent memory governance: the mandate gate, capture-author separation, trust-tier organization and convergence as a memory-state property. It claims no invention of shared memory, tiered memory, reflection, persistence or the underlying authorization mechanisms, and observed results are claimed only within their stated scope.

## 7. Implementations and Conformance

A database or service may implement the same properties as this illustrative reference layout:

```text
memory-repository/            memory-wide scope; .acm-root marks the top
  .acm-root
  mission.md                  intent: the mandate (principal-confirmed)
  learning.md                 trace: what this scope has learned so far; derived and replaceable
  records/<time>-<slug>.md    trace source: one file per record, identified by its path (R-18)
  tasks/<task-id>/            named task scope; the same three entries (under any scope)
  generated/                  optional, disposable reading aids (any scope); never required reading
  repositories/
    <repository-id>/          repository scope; the same three entries, plus:
      repository.json         the binding (R-13)
      audit-trail.md          legacy single-file trace, read-only
      evidence/               independently captured evidence, if implemented
      tasks/<task-id>/        named task scope
      paths/<repository-relative-path>/   path scope; the same three entries, and its own tasks/
```

Every scope has the same shape and all its files are optional until used. The governing chain is the nesting: memory-wide, repository, path, task; a scope's mandate narrows its parent's. A task inside one path lives under it; a task spanning paths lives at repository level, names those paths in its own `mission.md` and reads their mandates as peers (a conflict pauses the affected action, R-10); a task not tied to a repository lives at memory-wide level. A code folder named like a scope entry (`records`, `tasks`, `evidence`, `generated`, or any name ending in `.md`) or beginning with `_` is written under `paths/` with one extra leading `_` (names compared without regard to letter case), so entries and child paths never share a name and the mapping stays reversible.

An implementation is used by a person naming it in an ordinary prompt, after setup has supplied the binding; naming it cannot
compensate for missing configuration or permission enforcement. It states which requirements it does not meet, and may read and
append to an in-repository `.acm/` from an earlier version as a transitional legacy mode, which is not the separate memory
repository of R-12.

**Minimal and full conformance.** The contributor count is not a conformance mode; minimal and full differ in independent
evidence coverage, not in whether memory is solo or shared.

**Minimal conformance:** R-01 through R-03, R-05 through R-16 and R-18 through R-20 are implemented and their applicable checks
pass for the declared deployment. A conditional check may be marked not applicable only with the condition and a verifiable
reason that it is absent; this cannot waive an unconditional requirement. Evidence may be absent, but its absence MUST be
explicit; no independent-evidence or convergence claim follows. R-03 applies to evidence when available and always forbids
promoting trace into independent evidence.

**Full conformance:** minimal conformance plus independently enforced R-04 and the evaluation procedure in R-17. An
implementation can be conformant before a particular work item converges; it must not report convergence for that item until
the procedure passes. A local file pattern without enforced separation is an illustration, not structural conformance.

R-21 governs a claim about training use; an implementation declares whether it applies and cannot claim an unevaluated
extension. A conformance report states the version, the deployment boundary, the checks, the results, the limitations and the
policies that apply.

## 8. Shared Recording and Bounded Retrieval

**R-18.** Source recording MUST use the same collision-safe acceptance model for one writer and many. Every record MUST have an identifier unique within the memory source (in the reference layout its path, so the same time and slug in two scopes are two records). Acceptance MUST be atomic and create-only: an identical repeated submission is idempotent; a repeated identifier with different content MUST fail explicitly, never replace or line-merge. Concurrent distinct records MUST remain complete, attributable and individually retrievable. A file per record is a strategy, not sufficient protection alone; create-only acceptance detects collisions rather than assuming them impossible. Publishing a local record is distinct from its acceptance by the shared source. A legacy monolithic trail may be kept read-only; a migration accounts for every old record without rewriting it. Ordinary writers do not concurrently regenerate and commit the same derived surface.

**R-19.** Indexes, summaries, recent windows and learning files MUST be labelled derived and tied to the source revision or snapshot they represent. Before use, that revision and coverage MUST be validated against the bound source; stale or incomplete views MUST be regenerated or reported unusable, and filesystem, decoding or indexing errors MUST NOT silently omit scopes. Deleting a derived view MUST NOT lose source decisions or governing intent. Views are regenerated from a consistent accepted snapshot, never hand-merged; source changing during generation requires a new snapshot or a visible retry or failure. The governing mandates are read at the revisions R-09 requires, not replaced by summaries.

**R-20.** Retrieval MUST provide a bounded route from binding and governing mandates to the scope's current learning or index, then to relevant source and evidence references. A derived claim MUST identify its sources and author; a corrected claim MUST expose the known correction or supersession reference; recency MUST NOT by itself promote a claim to authority. Marker or date windows are a selection strategy, not a guarantee of meaning: where the bounded view cannot support an answer, escalate to source and state uncertainty.

**R-21.** Extraction for evaluation, training or tuning MUST have an explicit policy from the memory's principal defining allowed purposes, scope and access. Training permission MUST NOT be inferred from permission to read or record. Prefer independently captured observations and principal-confirmed outcomes where available; label trace-derived cases as self-report.

## Appendix A: Requirement Checks

Each row gives a falsifiable implementation check. No row asserts that its check has been executed. The rows state the main
falsifying tests, not every obligation of a requirement; an implementation that passes them can still violate one.
Natural-language interpretation, policy quality and useful relevance are not made impossible to get wrong by a schema.
Conformance claims must distinguish enforced properties from instructions that contributors are merely expected to follow.

| ID | Protection | Check |
|----|------------|-------|
| R-01 | Classification | Present a derived summary as independent evidence; it is rejected and its source tier recovered. |
| R-02 | Confirmation boundary | Submit unconfirmed agent text and an unauthorized peer update; neither becomes governing intent. Confirmed updates have identifiable authority and revisions. |
| R-03 | Conflict reporting | Seed an unauthorized observed action and a contradicting trace: both are preserved, the violation is reported and the trace is not promoted. |
| R-04 | Independent capture | Attempt agent filtering, alteration and deletion; reject each. Capture only after release (and, when streaming, after a portion was delivered); reject the ordering. Induce a capture failure and verify it is reported with the coverage gap. |
| R-05 | Create-only source | Modify or delete an accepted trace record; reject it. Accept a new correction referencing that record; regenerating derived views leaves both intact. |
| R-06 | Role separation | Exercise each write class under the wrong identity, including an unavailable acceptance service; fail visibly and preserve source. |
| R-07 | Access and policy | A reader of the enclosing scope cannot retrieve a restricted scope without separate permission. Withhold each applicable recording, access and retention policy in turn: recording cannot begin. |
| R-08 | Authorized exception | In a synthetic store, authorized erasure across declared replicas leaves a non-sensitive event; an unauthorized writer cannot erase; unreachable replicas are reported. |
| R-09 | Pre-work gate | A missing mandate, unread governing revision or unaccepted interpretation prevents governed action; agent-created text never authorizes itself. |
| R-10 | Authority resolution | A lower scope cannot widen authority; conflicting peers and a mid-work mandate change pause affected actions until authorized resolution. |
| R-11 | Explicit degraded state | Disconnect memory or trace acceptance: it is reported, dependent work pauses, no fallback memory is created, and prompt-authorized work is not labelled SACM-valid. |
| R-12 | Separate repository | Memory is a separately governed store; two work repositories each resolve to their own scope; a clean distribution of a work repository contains no development memory; a second writer needs no migration. |
| R-13 | Validated binding | Rename or move clones, use two same-named repositories, a fork sharing the root commit and conflicting bindings: clones and forks sharing a root commit resolve to one binding (another mechanism states its own fork rule); the rest keep identity or fail, never guessing. |
| R-14 | Intent-based scope | A deep path, an ambiguous task and a code-path move: old references stay resolvable and writes go only to the narrowest applicable scope; a code folder named like a scope entry maps to a distinct, reversible path. |
| R-15 | Containment and completeness | Parent escape, absolute paths, link redirects, an inaccessible parent and depth exhaustion cause no escaped access and no silent omission. |
| R-16 | Attributable records | Ordinary records carry scope, actor, authority, mandate revision, session, time, record identifier and verification references; only a preflight record may leave work identity or revision unresolved, and it grants no authority; later association preserves the original. |
| R-17 | Independent evaluation | A nonempty queue, an empty queue alone, an independent report with an actionable finding, or author self-review cannot produce convergence; a bounded report names unchanged artifacts and untested bars. |
| R-18 | Atomic concurrent acceptance | Concurrent distinct entries survive exactly once; identical retries are idempotent; identifier or content collisions fail without overwriting; no reader sees a partial record. |
| R-19 | Snapshot freshness | Change source during or after generation and inject unreadable files: stale or incomplete views are never reported current; removing derived files loses no source. |
| R-20 | Source-backed retrieval | Asked why a decision was reversed, a compressed view reaches the correction and original source, or reports insufficient context without inventing an answer; the context needed does not grow with unrelated history. |
| R-21 | Purpose authorization | Read permission alone cannot start an extraction pipeline; an allowed dataset keeps its source-tier and policy provenance. |

## Appendix B: Glossary

- **Principal:** the party with standing and an explicit decision rule to authorize a scope's work.
- **Mandate:** principal-confirmed what, why and constraints; proposed agent articulation is not yet a mandate.
- **Governed interval:** work after resolved mandates and a visible accepted interpretation pass the mandate gate.
- **Memory repository:** the separately identified and governed store holding memory for repositories of work.
- **Binding:** the explicit, validated association of work identity, memory identity and repository-level scope.
- **Scope:** a declared authority and relevance boundary within memory; not an access permission.
- **Derived view:** a replaceable index, window, summary or learning file (`learning.md`) with source provenance and snapshot identity; unrelated to training a model.
- **Acceptance boundary:** the mechanism that admits records and enforces roles, immutability and collision handling.
- **Capture-author separation:** the observed agent cannot author or suppress its independent observation record.
- **Convergence:** scoped work-queue exhaustion plus bounded independent evaluation with no findings, not an agent declaration; silence is such an evaluation's result.

*End of specification.*
