# Agent Context Memory (ACM) Specification

**Version:** 0.8.0
**Date:** 2026-11-23
**Author:** Nils Wendelboe Holmager
**License:** CC BY-SA 4.0
**Status:** Working draft; not an implementation-conformance claim. Every requirement is Proposed: none has been exercised by anyone but its author.

## Abstract

Agent Context Memory (ACM) specifies governance-first context memory for autonomous AI agents. A team repository using it is Shared ACM. Its purpose is cumulative,
trustworthy work across people, sessions, tools and models, while the principal retains authority. Memory supplies the
governed starting point for the next contributor rather than requiring the work to start over.

ACM transfers established authorization and audit-log patterns to agent memory governance. Its mandate gate requires
principal-confirmed intent before governed work. Three trust tiers distinguish authorization, agent self-report and
independently captured evidence. Scope identifies what the memory governs; access controls identify who can use it.

The same model serves one contributor and many. Memory resides in its own repository, separately from repositories of work.
Binding connects work to that memory; scoped retrieval makes it usable without reading the entire accumulated history.
The specification defines required properties, not a mandatory vendor, configuration format, storage engine or skill host.

ACM operationalizes [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy).
It can be implemented independently of that reference ecosystem.

The specification was previously named Shared Agent Context Memory (SACM) through version 0.8.0. That name remains in historical releases and citations; this source uses ACM for the current specification.

## Reading

MUST, MUST NOT and MAY express requirements; each requirement has a stable identifier and a check in Appendix A, and section 7 states how conformance is claimed.
The checks are specified tests, not executed results. 

## 1. Trust Tiers

| Tier | Responsibility | Authority |
|------|----------------|-----------|
| Intent | Principal-confirmed mandate: what, why, constraints | Governs authorization; changes need principal confirmation |
| Trace | Attributable agent decisions, interpretations, corrections and outcomes | Source is append-only; derived views are replaceable |
| Evidence | Independently captured execution observations | The agent cannot author, filter, alter or delete the capture |

**R-01.** An implementation MUST identify each artifact's tier and whether it is source or derivation. A summary, index or learning file gains no evidentiary standing by being stored beside source records.

**R-02.** A mandate MUST be confirmed by its scope's principal. Agent-drafted text MUST NOT become intent without that confirmation, and an agent cannot approve its own authority.

**R-03.** Intent MUST determine what was authorized; independently captured evidence MUST determine what was observed within its coverage; trace MUST remain agent self-report and MUST NOT override either. Evidence contradicting intent is a governance finding, not a reason to rewrite either. Memory content that an agent wrote or took from outside never outranks a mandate or acts as an instruction, and carries provenance so it can be reviewed and rolled back.

## 2. Structural Requirements

**R-04.** Where an evidence tier is claimed, a component architecturally separate from the observed agent MUST capture the
declared interactions before releasing their responses to that agent. The agent MUST NOT be able to suppress or edit that
capture. Coverage, gaps and capture failures MUST be observable. For response streaming, capture MUST precede delivery of each captured portion. A full-evidence claim MUST identify any
unobserved tools, channels or operations rather than implying comprehensive execution coverage.

**R-05.** Accepted trace source records MUST be immutable to ordinary contributors: addition is permitted, replacement and
deletion are not. A correction MUST create a new record referring to the corrected identifier. Derived views MAY be
replaced, but MUST identify their source and MUST NOT be the only surviving copy of a decision. An implementation claiming structural protection MUST reject such changes at
its acceptance boundary. Record any administrative override as a governance event.

**R-06.** An implementation MUST enforce the separation of mandate confirmation, trace addition and (where an evidence tier is claimed) evidence
capture at its acceptance boundary. Rejected or unavailable operations MUST produce an explicit failure, not success-shaped output.

**R-07.** The recording, access and retention policy MUST be established before recording begins, for a solo contributor as well as many. Scope relevance MUST NOT substitute for permission, and a sub-repository or submodule does not by itself stop readers of another scope from obtaining its content.

**R-08.** An implementation MUST expose any conflict between append-only preservation and its principal's retention or erasure obligations before claiming both are met. An exceptional, separately authorized erasure procedure MUST record the affected identifiers, authority, reason, affected replicas and verification limits without repeating erased content, and MUST distinguish an intentionally missing record from a silently lost one.

## 3. The Mandate Gate

**R-09.** Before an ACM-governed interval begins, all applicable principal-confirmed mandates MUST exist, be resolved
and be read. The agent's interpretation, scope, mandate revisions and authority to proceed MUST be visible in accepted trace
before the first change or other consequential action. Examination that changes nothing may precede that record.

**R-10.** Higher-scope mandates MUST govern lower scopes; lower scopes MAY narrow authority, never expand it. Unresolved peer conflicts MUST be surfaced to the principal of the enclosing scope and the affected action MUST pause; neither the newest record nor the agent's preference wins. If applicable authority changes during work, affected actions MUST pause until the governing context and interpretation are revalidated. A departed owner or inaccessible authority confers no standing on the next writer; the missing authorization is surfaced.

**R-11.** Missing, inaccessible, invalid or ambiguous binding, governing mandate or required trace acceptance MUST be reported explicitly, and dependent work MUST pause. Work independently authorized by the current prompt and available governing context MAY continue but MUST NOT be represented as a valid ACM-governed interval without passing R-09. The implementation MUST NOT claim unavailable memory was read, invent a replacement mandate, silently switch memory repositories or silently create a competing local trace; records may be queued only if policy permits and are not represented as accepted shared records.

## 4. Memory Repository and Scopes

**R-12.** Memory MUST reside in a repository of its own, separate from the repositories of work (an identified, governed store with its own acceptance and access boundary, not necessarily Git). Each repository of work MUST have a repository-level scope in it; one memory MAY hold many. Adding contributors MUST NOT require a different storage model or a migration. A distribution of a work repository MUST NOT contain its development memory.

**R-13.** Setup MUST establish an explicit binding between the repository of work, the memory repository and the repository-level scope, once per deployment rather than in prompts, and the implementation MUST validate it before memory use. Folder names and remote basenames alone MUST NOT establish identity; relocating or renaming a clone MUST NOT silently change the binding; ambiguous, conflicting or mismatched bindings MUST fail visibly, with no guessed read or write. A fork or copy MUST NOT silently inherit another repository's binding.

**R-14.** Within the validated binding, repository-relative code locations MUST resolve deterministically to their path scopes; named feature, change-request or task scopes MUST be selected from established task intent, not guessed from a path; a code path named like a scope entry MUST map to a distinct, reversible path scope; ambiguity MUST be surfaced before writing. A record goes to the narrowest applicable scope: the task scope the established task names, else the path scope containing the work, else the repository scope, else (work not tied to a repository) the memory-wide scope. Moves of scopes or code paths need an explicit old-to-new mapping, and existing references MUST remain resolvable.

**R-15.** Resolution MUST return every applicable governing scope, broadest first, with the active write scope. It MUST stop at the principal-declared memory ceiling, apply path containment and the declared access boundaries, report any it could not apply, and not leave the binding through parent segments, absolute paths, symbolic links or equivalent redirects. If a safety bound prevents complete resolution it MUST report failure, not drop governing scopes silently.

**R-16.** Each source record MUST identify its scope (or explicit memory-wide context), the identities of the covered repositories of work, actor, principal or authority reference, applicable mandate revisions, work or session identifier, record identifier, format version, time, relevant verification references and, where it records a decision or a reversal, a marker. Agent or model identity MUST be stated where known, otherwise "unknown", never invented. Asserted identity is recorded separately from authenticated identity. A preflight observation made before work identity or revisions are established marks those fields unresolved, which grants no authority; later association is recorded without rewriting the original.

## 5. Convergence

**R-17.** Convergence MUST require both an exhausted work queue for the declared scope and independent evaluation finding
nothing further to change against its declared quality bar. The agent's self-assessment or a passing automated check alone
MUST NOT establish convergence. An evaluation identifies the artifact revision, examined and untested surfaces, evaluator
identity and independence, and whether it is fresh or a same-session follow-up.

## 6. Prior Art and Contribution

ACM builds on three capability-first memory models: Sumers et al. (2023), *Cognitive Architectures for Language Agents* ([arXiv:2309.02427](https://arxiv.org/abs/2309.02427)), which distinguishes functional memory types; Park et al. (2023), *Generative Agents* ([arXiv:2304.03442](https://arxiv.org/abs/2304.03442)), the observation, reflection and planning precedent; and Packer et al. (2023), *MemGPT* ([arXiv:2310.08560](https://arxiv.org/abs/2310.08560)), hierarchical memory and cross-session persistence. It keeps those capabilities and adds governance: authorship, authority and verifiability, with reflection as a derivation distinct from source evidence and confirmed intent. Role-based access control models (Sandhu et al., 1996, IEEE Computer 29(2)), MAC and capability models establish authorization before action and separation of actor from authority; independent capture and append-only source apply established audit-log patterns. These analogies motivate ACM; they do not prove that natural-language mandates are machine-enforceable.

Its stated contribution is transferring established authorization patterns to agent memory governance: the mandate gate, capture-author separation, trust-tier organization and convergence as a memory-state property. It claims no invention of shared memory, tiered memory, reflection, persistence or the underlying authorization mechanisms, and observed results are claimed only within their stated scope.

## 7. Implementations and Conformance

A database or service may implement the same properties as this illustrative reference layout:

```text
memory-repository/            memory-wide scope; .acm-root marks the top
  .acm-root
  goal.md                  intent: the mandate (principal-confirmed)
  learning.md                 trace: what this scope has learned so far; derived and replaceable
  records/<time>-<slug>.md    trace source: one file per record, identified by its path (R-18)
  tasks/<task-id>/            named task scope; the same three entries (under any scope)
  generated/                  optional, disposable reading aids (any scope); never required reading
  repositories/
    <repository-id>/          repository scope; the same three entries, plus:
      repository.json         repository identity, for the binding (R-13)
      evidence/               independently captured evidence, if implemented
      tasks/<task-id>/        named task scope
      paths/<repository-relative-path>/   path scope; the same three entries, and its own tasks/
```

Every scope has the same shape and all its files are optional until used. The governing chain is the nesting: memory-wide, repository, path, task; a scope's mandate narrows its parent's. A task inside one path lives under it; a task spanning paths lives at repository level, names those paths in its own `goal.md` and reads their mandates as peers (a conflict pauses the affected action, R-10); a task not tied to a repository lives at memory-wide level. A code folder named like a scope entry (`records`, `tasks`, `evidence`, `generated`, or any name ending in `.md`) or beginning with `_` is written under `paths/` with one extra leading `_` (names compared without regard to letter case), so entries and child paths never share a name and the mapping stays reversible.



**Minimal and full conformance.** The contributor count is not a conformance mode; minimal and full differ in independent
evidence coverage, not in whether memory is solo or shared.

**Minimal conformance:** R-01 through R-03, R-05 through R-16 and R-18 through R-20 are implemented and their applicable checks
pass for the declared deployment. A check may be marked not applicable only with the condition that makes it so (for example, no evidence tier is claimed) and a
verifiable reason that the condition holds; this cannot waive an unconditional requirement. Evidence may be absent, but its absence must be
explicit; no independent-evidence or convergence claim follows. R-03 applies to evidence when available and always forbids
promoting trace into independent evidence.

**Full conformance:** minimal conformance plus independently enforced R-04 and the evaluation in R-17. An
implementation can be conformant before a particular work item converges; it must not report convergence for that item until
the procedure passes. A file pattern without enforced separation does not meet R-05 and R-06.

R-21 governs a claim about training use; an implementation declares whether it applies. A conformance report states the version, the deployment boundary, the checks, the results, the limitations and the
policies that apply.

## 8. Shared Recording and Bounded Retrieval

**R-18.** Source recording MUST use the same collision-safe acceptance model for one writer and many. Every record MUST have an identifier unique within the memory repository (in the reference layout its path, so the same time and slug in two scopes are two records). Acceptance MUST be atomic and create-only: an identical repeated submission is idempotent; a repeated identifier with different content MUST fail explicitly, never replace or line-merge. Concurrent distinct records MUST remain complete, attributable and individually retrievable. Publishing a local record is distinct from its acceptance by the shared memory repository.

**R-19.** Indexes, summaries, recent windows and learning files MUST be labelled derived and tied to the source revision or snapshot they represent. Before use, that revision and coverage MUST be validated against the bound source by comparing the recorded revision, not by rescanning the source; stale or incomplete views MUST be regenerated or reported unusable, and filesystem, decoding or indexing errors MUST NOT silently omit scopes. Deleting a derived view MUST NOT lose source decisions or governing intent. Views are regenerated from a consistent accepted snapshot, never hand-merged; source changing during generation requires a new snapshot or a visible retry or failure. A derived claim carries its date; a view changes by small sourced items, not by whole rewrites, and stays unchanged while what it says is unchanged. A superseded or no longer valid claim is retired from the current view  and stays in source.

**R-20.** Retrieval MUST provide a bounded route from binding and governing mandates to the scope's current learning or index, then to relevant source and evidence references. A derived claim MUST identify its sources and author; a corrected claim MUST expose the known correction or supersession reference; recency MUST NOT by itself promote a claim to authority. Where the bounded view cannot support an answer, escalate to source and state uncertainty. The amount read to start a task MUST be stated and bounded by the governing mandates and the scope's current learning; its cost, and the cost of recording, keeping fresh and auditing, MUST NOT grow with storage, writers or age, and an adopter MUST be able to observe what a task reads. A person MUST be able to audit through marked reversals and decisions, derived views and sampled source, so that audit cost does not grow with the number of records.

**R-21.** Extraction for evaluation, training or tuning MUST have an explicit policy from the memory's principal defining allowed purposes, scope and access. Training permission MUST NOT be inferred from permission to read or record. Trace-derived cases MUST be labelled self-report.

## Appendix A: Requirement Checks

Each row gives a falsifiable implementation check.

| ID | Protection | Check |
|----|------------|-------|
| R-01 | Classification | Present a derived summary as independent evidence; it is rejected and its source tier recovered. |
| R-02 | Confirmation boundary | Submit unconfirmed agent text, and an agent's approval of its own text: neither becomes governing intent. |
| R-03 | Conflict reporting | Seed an unauthorized observed action and a contradicting trace: both are preserved, the violation is reported and the trace is not promoted; agent-written memory content never outranks a mandate, is not obeyed as an instruction, and names its provenance. |
| R-04 | Independent capture | Attempt agent filtering, alteration and deletion; reject each. Capture only after release (and, when streaming, after a portion was delivered); reject the ordering. Induce a capture failure and verify it is reported with the coverage gap; a full-evidence claim lists the tools and channels not observed. |
| R-05 | Create-only source | Modify or delete an accepted trace record; reject it. Accept a new correction referencing that record; regenerating derived views leaves both intact; an administrative override is recorded as a governance event. |
| R-06 | Role separation | Exercise each write class under the wrong identity, including an unavailable acceptance service; fail visibly and preserve source. |
| R-07 | Access and policy | A reader of the enclosing scope cannot retrieve a restricted scope without separate permission. Withhold each applicable recording, access and retention policy in turn: recording cannot begin. |
| R-08 | Authorized exception | In a synthetic store, authorized erasure across declared replicas leaves a non-sensitive event; an unauthorized writer cannot erase; unreachable replicas are reported; a store claiming both append-only and erasure without exposing the conflict is rejected; an intentionally missing record is distinguishable from a lost one. |
| R-09 | Pre-work gate | A missing mandate, unread governing revision or unaccepted interpretation prevents governed action; read-only examination before the record is allowed. |
| R-10 | Authority resolution | A lower scope cannot widen authority; conflicting peers and a mid-work mandate change pause affected actions until authorized resolution; an unresolved peer conflict goes to the enclosing scope's principal; a departed owner's mandate gives the next writer no standing. |
| R-11 | Explicit degraded state | Disconnect memory or trace acceptance: it is reported, dependent work pauses, no fallback memory is created, prompt-authorized work is not labelled ACM-valid, and queued records are not represented as accepted. |
| R-12 | Separate repository | Memory is a separately governed store; two work repositories each resolve to their own scope; a clean distribution of a work repository contains no development memory; a second writer needs no migration. |
| R-13 | Validated binding | The binding is established at setup, not in prompts. Rename or move clones, use two same-named repositories, a fork or copy and conflicting bindings: each keeps its identity or fails, a fork or copy never silently inherits another binding, and nothing is guessed. |
| R-14 | Intent-based scope | A deep path, an ambiguous task and a code-path move: old references stay resolvable and writes go only to the narrowest applicable scope; a code folder named like a scope entry maps to a distinct, reversible path. |
| R-15 | Containment and completeness | Parent escape, absolute paths, link redirects, an inaccessible parent and depth exhaustion cause no escaped access and no silent omission; resolution stops at the ceiling, returns governing scopes broadest first with the write scope, and reports what it could not apply. |
| R-16 | Attributable records | Ordinary records carry scope, covered repositories, actor ("unknown", never invented), authority, mandate revision, session, time, record identifier, format version, a marker on decisions and reversals and verification references; asserted and authenticated identity are recorded separately; only a preflight record may leave work identity or revision unresolved, and it grants no authority; later association preserves the original. |
| R-17 | Independent evaluation | A nonempty queue, an empty queue alone, an independent report with an actionable finding, author self-review, or a passing automated check alone cannot produce convergence; a bounded report names the artifact revision, examined and untested surfaces, and the evaluator's identity and independence, and whether it is fresh or a same-session follow-up. |
| R-18 | Atomic concurrent acceptance | Concurrent distinct records survive exactly once; identical retries are idempotent; identifier or content collisions fail without overwriting; no reader sees a partial record; a local record is not reported accepted until the memory repository accepts it. |
| R-19 | Snapshot freshness | Change source during or after generation and inject unreadable files: stale or incomplete views are never reported current; removing derived files loses no source; a derived claim without its date, or a wholesale rewrite of a view whose sources are unchanged, or a hand-merged view, is rejected; validation compares the recorded revision without rescanning source; a superseded claim is absent from the current view and present in source. |
| R-20 | Source-backed retrieval | Asked why a decision was reversed, a compressed view reaches the correction and original source, and each derived claim names its sources and author, or reports insufficient context without inventing an answer; a newer claim is not preferred merely for being newer; an agent given only the binding reaches the mandates and the current learning; the amount a task reads at start is stated and observable, and unrelated records, writers or years change neither it nor the cost of recording or refreshing views; a person can audit through reversals, decisions and sampled source without reading every record. |
| R-21 | Purpose authorization | Read permission alone cannot start an extraction pipeline; an allowed dataset keeps its source-tier and policy provenance; trace-derived cases are labelled self-report. |

## Appendix B: Glossary

- **Principal:** the party that confirms a scope's mandate.
- **Mandate:** principal-confirmed what, why and constraints; proposed agent articulation is not yet a mandate.
- **Governed interval:** work after resolved mandates and a visible accepted interpretation pass the mandate gate.
- **Repository of work:** a repository whose work the memory governs; its development memory lives in the memory repository, not in it.
- **Memory repository:** the separately identified and governed store holding memory for repositories of work.
- **Binding:** the explicit, validated association of work identity, memory identity and repository-level scope.
- **Scope:** a declared authority and relevance boundary within memory; not an access permission.
- **Derived view:** a replaceable index, window, summary or learning file (`learning.md`) with source provenance and snapshot identity.
- **Path scope, task scope:** the scope of a code location within a repository of work, and the scope of a named task.
- **Ceiling:** the top of the memory, declared by its principal (in the reference layout, the `.acm-root` marker); resolution never looks above it.
- **Marker:** a label on a record that flags a decision or a reversal, so that views can select it.
- **Acceptance boundary:** the mechanism that admits records and enforces roles, immutability and collision handling.
- **Capture-author separation:** the observed agent cannot author or suppress its independent observation record.
- **Convergence:** scoped work-queue exhaustion plus bounded independent evaluation with no findings, not an agent declaration.

*End of specification.*
