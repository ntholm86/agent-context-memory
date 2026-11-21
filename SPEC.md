# Agent Context Memory Specification

**Version:** 0.5.0
**Date:** 2026-11-21
**Author:** Nils Wendelboe Holmager
**License:** CC BY-SA 4.0
**Status:** Published specification; not an implementation-conformance claim. Each requirement states its own evidence status.
**Baseline:** Published ACM v0.4.0 (commit 51e951e of this repository).

## Abstract

Agent Context Memory (ACM) specifies governance-first context memory for autonomous AI agents. Its purpose is cumulative,
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

## Reading and requirement status

MUST, MUST NOT, SHOULD and MAY express conformance requirements. Each requirement has a stable identifier and a check
in Appendix A. Normative wording describes the proposed standard; it does not assert that an implementation exists or passes it.

Evidence status is separate from author approval:

- **Inherited:** a property of published v0.4.0, not a fresh demonstration of enforcement.
- **Proposed:** a design requirement awaiting implementation and the named checks.
- **Observed:** demonstrated only in an identified experiment, under its stated limits.
- **Deferred:** an unresolved matter with its reason and reopening evidence recorded.

The checks in this specification are specified tests, not executed test results. A mechanical check of document structure or
version consistency does not establish ACM conformance, privacy, useful retrieval or correct agent behaviour.

## 1. Trust Tiers

### 1.1 Intent, trace and evidence

ACM organizes memory by authorship and trust, independently of functional memory types.

| Tier | Responsibility | Authority and mutability |
|------|----------------|--------------------------|
| Intent | Principal-confirmed mandate: what, why and constraints | Governs authorization; changes require principal confirmation |
| Trace | Attributable agent decisions, interpretations, corrections and outcomes | Source records are append-only; derived views are replaceable |
| Evidence | Independently captured execution observations | Agent cannot author, filter, alter or delete the capture |

**R-01.** An implementation MUST identify each artifact's tier and whether it is source or derivation. A summary, index or
orientation does not gain independent evidentiary standing merely by being stored beside source records.

### 1.2 Mandate ownership and articulation

**R-02.** Each governing scope MUST identify its principal and the mechanism by which that principal confirms a mandate or
delegates authority. A principal may be a person, role or collective with an explicit decision rule. Agent-drafted candidate
text MUST NOT become intent without that confirmation. The principal owns the meaning, not necessarily all the typing.
Confirmation and mandate revisions MUST be identifiable to later readers.

An agent may assist in articulation. It cannot approve its own authority, infer approval from silence, or silently replace
a mandate. Changes made by the principal do not retroactively authorize earlier actions.

### 1.3 What each tier establishes

**R-03.** Intent MUST determine what was authorized; independently captured evidence MUST determine what was observed within
its capture coverage; trace MUST remain agent self-report. Evidence contradicting intent is a governance finding, not a
reason to rewrite either. Trace MUST NOT override either.

There is no universal ranking that makes an observed action become an authorized action. Evidence of an API call does not
prove its intended outcome, and an LLM transcript does not independently prove tool execution unless the capture includes it.
Record capture gaps. Conflicting observations require investigation, not an agent's unmarked preference.

### 1.4 Functional memory and skills

Working, episodic, semantic and procedural memory can coexist with the trust axis. Functional classifications do not
grant authorship rights. A recalled decision is not automatically a new instruction.

Useful skills may be distilled from accumulated learning. That is an awareness, not an ACM skill-generation function.
ACM does not prescribe where skills live or how a principal adopts them.

## 2. Structural Requirements

### 2.1 Capture-author separation

**R-04.** Where an evidence tier is claimed, a component architecturally separate from the observed agent MUST capture the
declared interactions before releasing their responses to that agent. The agent MUST NOT be able to suppress or edit that
capture. Coverage, gaps and capture failures MUST be observable. Reconstructed self-report is not independent evidence.

For response streaming, capture must precede delivery of each captured portion. A full-evidence claim MUST identify any
unobserved tools, channels or operations rather than implying comprehensive execution coverage.

### 2.2 Append-only source, replaceable derivations

**R-05.** Accepted trace source records MUST be immutable to ordinary contributors: addition is permitted, replacement and
deletion are not. Sealing a closed period under R-25 is repackaging, not deletion, while every record's bytes stay verifiable.
A correction MUST create a new record referring to the corrected identifier. Derived views MAY be
replaced, but MUST identify their source and MUST NOT be the only surviving copy of a decision.

Git history and an instruction saying "do not edit" are not alone enforcement: a later commit can change an old file.
An implementation claiming structural protection must reject such changes at its acceptance boundary. Record any
administrative override as a governance event. Chronological presentation does not prove causal order between concurrent writers.

### 2.3 Permission enforcement

**R-06.** An implementation MUST enforce the separation of mandate confirmation, trace addition and evidence capture at its
acceptance boundary. Rejected or unavailable operations MUST produce an explicit failure, not success-shaped output.
If protection depends on instructions rather than enforcement, disclose that limitation and do not claim structural conformance.

**R-07.** The applicable recording, access and retention policy MUST be established before recording begins, for a solo
contributor as well as many. Scope relevance MUST NOT substitute for permission. A sub-repository or submodule MAY provide
packaging, but does not by itself prevent readers of another scope from obtaining its content.

Recording policy defines permissible content and evidence references. Secret detection can supplement that policy; no scan
is claimed to prove that arbitrary natural-language records contain no confidential or personal information.

### 2.4 Retention and exceptional erasure

Ordinary contributors cannot erase accepted source under R-05. This does not grant unlimited retention authority.

**R-08.** An implementation MUST expose any conflict between append-only preservation and its principal's retention or
erasure obligations before claiming both are met. An exceptional, separately authorized erasure procedure MUST document
the affected record identifiers, authority, reason, affected replicas and verification limits without repeating erased content.
It MUST distinguish an intentionally missing record from a silently lost record.

This is a proposed exception to the v0.4.0 statement that no party can ever change captured evidence. It does not authorize
erasure here. An adopter determines lawful retention and erasure; ACM supplies no legal opinion or guarantee that every
backup, cache or public clone can be recalled.

## 3. The Mandate Gate

### 3.1 Governed work interval

**R-09.** Before an ACM-governed work interval begins, all applicable principal-confirmed mandates MUST exist, be resolved
and be read. The agent's interpretation, scope, mandate revisions and authority to proceed MUST be visible in accepted trace
before the first change or other consequential action. Examination that changes nothing may precede that record, and its
findings are recorded afterward. Binding validation and mandate reading are preflight, not authorization to alter the work.

A host conversation can contain setup or mandate articulation before a governed interval. Such activity does not retroactively
validate earlier unauthorized work. The governed interval starts only after the gate passes; an agent cannot authorize itself
by creating a mandate mid-work. This makes the boundary explicit rather than relying on harness-specific session naming.

### 3.2 Changing or conflicting authority

**R-10.** Higher-scope mandates MUST govern lower scopes within the declared hierarchy. Lower scopes MAY narrow authority,
not expand it. Unresolved peer conflicts MUST be surfaced to the designated authority; neither the newest record nor the
agent's preference determines the winner. If applicable authority changes during work, affected actions MUST pause until
the governing context and visible interpretation are revalidated.

A shared principal needs an explicit decision rule, not a fixed number of people. A departed owner or inaccessible authority
does not confer standing on the next writer. The implementation must surface the missing authorization.

### 3.3 Unavailable memory

**R-11.** Missing, inaccessible, invalid or ambiguous binding, governing mandate or required trace acceptance MUST be
reported explicitly. Work depending on it MUST pause. Work independently authorized by the current prompt and available
governing context MAY continue, but MUST NOT be represented as a valid ACM-governed interval without passing R-09.

The implementation MUST NOT claim unavailable memory was read, invent a replacement mandate, silently switch memory sources,
or silently create a competing local trail. A deployment may queue records for the same bound destination only if its policy
explicitly permits that behaviour; queued records are not represented as accepted shared records.

## 4. Memory Repository and Scopes

### 4.1 One operational model

**R-12.** Memory MUST reside in a repository of its own, separate from the repositories of work. A repository of work MUST
have a repository-level memory scope; one memory repository MAY hold many such scopes. Adding contributors MUST NOT require
a different storage model or migration from a private-only trace layout.

Here repository means the identified, governed memory store, not a required Git implementation. It needs its own identity,
acceptance and access boundary. Its physical hosting is an implementation choice. Public specification distribution must
not implicitly distribute its development memory.

### 4.2 Explicit binding and stable identity

**R-13.** Setup MUST establish an explicit binding between the repository of work, memory source and repository-level scope.
The implementation MUST validate that binding before memory use. Local folder names and remote basenames alone MUST NOT
establish identity. Clone relocation or renaming MUST NOT silently change the binding. Ambiguous, conflicting or mismatched
bindings MUST fail visibly without reading or writing a guessed alternative.

Binding is configured once for the applicable deployment/workspace/repository rather than repeated in ordinary prompts.
Aliases after a repository rename require an explicit migration of the binding; a remote URL is a locator, not immutable identity.
An implementation may use authenticated host identifiers, explicit identifiers or another verifiable binding mechanism.
Where the binding mechanism is the root commit, as in the reference implementation, it identifies shared history, not
governance: clones and forks sharing a root commit are one work identity and share one binding. Governing a fork separately requires another confirmed discriminator, never a guess.

### 4.3 Path scopes and named scopes

**R-14.** Within the validated repository binding, repository-relative code locations MUST resolve deterministically to
their applicable path scopes. Named feature, change-request or task scopes MUST additionally be selected from established
task intent and scope-purpose metadata, not guessed from a code path. Ambiguity MUST be surfaced before writing.

The memory mirrors repository-relative paths while retaining explicit task scopes where directory structure is insufficient.
The active scope and changed paths are recorded. A record goes to the narrowest applicable scope: a task scope the established
task names, else the path scope containing the work, else the repository scope, else (for work not tied to a repository) the memory-wide scope (of the one registered memory, or of the one the principal names when several are registered). Work spanning several scopes is recorded in the authorized coordinating
scope or through explicitly linked records; no unexplained duplication of a decision across trails.
Scope or code-path moves require an explicit old-to-new mapping; existing references must remain resolvable.

### 4.4 Governing chain and ceiling

**R-15.** Resolution MUST return every applicable governing scope in the declared chain, broadest first, together with the
active write scope. It MUST stop at the principal-declared memory ceiling and enforce path containment and access boundaries.
It MUST NOT traverse outside the binding through parent segments, absolute-path injection, symbolic links or equivalent redirects.
If an implementation safety bound prevents complete resolution, report failure rather than silently dropping governing scopes.

Scope hierarchy is explicit governance structure, not inference from arbitrary neighbouring clone folders. A scope's required
but inaccessible higher mandate blocks affected governed work; it does not justify proceeding with lower mandates alone.

### 4.5 Attribution and evidence placement

**R-16.** Each source record MUST identify its scope, covered work-repository identities or explicit memory-wide context,
actor, principal/authority reference, applicable
mandate revisions, work/session identifier, record identifier, time and relevant verification references. Agent/model
identity MUST be stated where known; otherwise state unknown, not an invented model name.

Record asserted identity separately from authenticated identity. A Git author field alone is not authentication.
For preflight observations before work identity or mandate revisions are established, mark those fields unresolved and retain
the capture's memory/session identity. Mark genuinely inapplicable fields explicitly. Neither case authorizes governed work;
later association with the validated work context is recorded without rewriting the original observation.
Evidence is associated with the scope of the documented actions. A cross-scope session may have one canonical capture with
scope-specific references, rather than copied captures that imply separate observations.

## 5. Convergence

### 5.1 Completion and silence

**R-17.** Convergence MUST require both an exhausted work queue for the declared scope and independent evaluation finding
nothing further to change against its declared quality bar. The agent's self-assessment or a passing automated check alone
MUST NOT establish convergence.

Evaluation identifies the artifact revision/hash, examined surfaces, untested surfaces, evaluator identity and independence,
and whether the evaluation is fresh or a same-session follow-up. A different model family reduces one source of shared bias;
it is not proof of statistical independence. Principal review has role independence, not necessarily organizational independence.

Silence is bounded, provisional evidence of no findings, not proof that none exist. Deferred items outside an evaluated
scope remain visible; deferring an unresolved item inside the claimed scope is not the same as completing it.

### 5.2 Relationship to PEA

The mandate gate implements Operator's Intent; trace and capture separation implement Observable Autonomy; bounded
independent evaluation implements Convergence Is Silence. The principal defines what and why. The agent chooses how
within that authority. Memory carries the track record against which greater delegation can be earned.

## 6. Prior Art and Contribution

### 6.1 CoALA

Sumers, T. R., Yao, S., Narasimhan, K., and Griffiths, T. L. (2023), *Cognitive Architectures for Language Agents*,
[arXiv:2309.02427](https://arxiv.org/abs/2309.02427), distinguishes functional memory types. ACM retains that functional
axis and adds governance by authorship, authority and verifiability. It does not claim to invent functional or tiered memory.

### 6.2 Generative Agents

Park, J. S., et al. (2023), *Generative Agents: Interactive Simulacra of Human Behavior*,
[arXiv:2304.03442](https://arxiv.org/abs/2304.03442), supplies the observation/reflection/planning precedent and retrieval
by relevance, recency and importance. ACM uses reflection as derivation while distinguishing a reflection from source
evidence and principal-confirmed intent.

### 6.3 MemGPT

Packer, C., et al. (2023), *MemGPT: Towards LLMs as Operating Systems*,
[arXiv:2310.08560](https://arxiv.org/abs/2310.08560), supplies hierarchical memory and cross-session persistence.
ACM builds on those capabilities; authority boundaries and source-preserving derivation are additional governance concerns.

### 6.4 Authorization and audit-log patterns

Sandhu, R. S., Coyne, E. J., Feinstein, H. L., and Youman, C. E. (1996), *Role-Based Access Control Models*, is an
authorization precedent. RBAC, MAC and capability models establish authorization before action and separation of actor
from authorizing authority. Independent capture and append-only source apply established audit-log patterns.
These analogies motivate ACM; they do not prove that natural-language mandates are machine-enforceable or that ACM replaces
per-operation access checks. Runtime governance and training-time alignment address different questions and can coexist.

### 6.5 Scale and governance

The v0.4.0 Scale discussion introduced bounded recent windows plus archives regenerated from source. This version carries
that lesson into shared recording and retrieval in Section 8. No claim that generation alone prevents stale caches is made:
freshness needs a validated source revision and a completeness check.

### 6.6 Contribution claims

ACM's stated contribution is transferring established authorization patterns to agent memory governance: the mandate gate,
capture-author separation, trust-tier organization and convergence as a memory-state property.
It claims no invention of shared memory, tiered memory, reflection, persistence or the underlying authorization mechanisms.
Observed results are claimed only within their stated experiment scope.

### 6.7 Augmented Individual Intelligence

Holmager (2026), *Augmented Individual Intelligence*,
[DOI:10.5281/zenodo.18417872](https://doi.org/10.5281/zenodo.18417872), includes agency preservation among its boundary
conditions. ACM supplies governance infrastructure for that goal: retained principal authority, attributable decisions and
independent observations. The existence of files or a conforming record schema alone does not prove preserved human agency.

## 7. Reference Implementation and Conformance

### 7.1 Separate memory, ordinary entry skill

The reference route is the PEA skills suite or a Work entry skill. The user names the skill in an ordinary prompt; setup
has already supplied the binding. The skill resolves the scope, reads governing intent and bounded relevant context,
makes its interpretation visible, acts within authority and records outcomes to the bound scope.
Naming a skill cannot compensate for absent deployment configuration or permission enforcement.

This is a reference contract, not a claim that currently released reference skills implement it.

### 7.2 File layout

The following layout is illustrative. A database or service may implement the same properties.

```text
memory-repository/            memory-wide scope; .acm-root marks the top
  .acm-root
  destination.md              intent: the mandate (principal-confirmed)
  orientation.md              trace, derived and replaceable
  records/<time>-<slug>.md      trace source: records, one file each, identified by their path (R-18); closed months sealed (R-25)
  tasks/<task-id>/            named task scope; the same three entries (a task may sit under any scope)
  repositories/
    <repository-id>/          repository scope; the same three entries, plus:
      repository.json         the binding (R-13)
      audit-trail.md          legacy single-file trace, read-only, kept after migration
      evidence/               independently captured evidence, if implemented
      tasks/<task-id>/        named task scope; the same three entries
      paths/<repository-relative-path>/
                              path scope; the same three entries, and its own tasks/
```

Every scope is a folder with the same shape; a scope's files are all optional until used. Generated views (indexes,
summaries, recent windows) are not part of the layout: they are regenerated on demand from the records (R-19). The one stored
derived view is `orientation.md`, labelled derived (R-19).

The governing chain is the folder nesting: memory-wide, repository, path, task. A scope's mandate narrows its parent's and never contradicts it. A task that works inside one path lives under that path; a task that spans paths lives at repository level and names those paths in its own `destination.md`; a task not tied to one
repository lives at memory-wide level. A task at repository level also reads the mandates of the paths it names, as peers: a conflict between them pauses the affected action until authorized resolution (R-10). A code folder named like one of a scope's own entries (`records`, `tasks`, `evidence`, or any name ending in `.md`), or beginning with `_` (names compared without regard to letter case), is written under `paths/` with one extra leading `_` and its original spelling, so a scope's entries and its child paths never share a name and the mapping stays reversible. The binding maps repository identity to its namespace.
Labels such as `<repository-id>` are not instructions to derive identity from a folder basename.
An implementation can place restricted scopes in separate repositories with independent access controls.

### 7.3 Minimal and full conformance

The contributor count is not a conformance mode. The inherited distinction between minimal and full concerns independent
evidence coverage, not whether memory is solo or shared.

**Minimal conformance:** R-01 through R-03 and R-05 through R-16, plus R-18 through R-20 and R-25, are implemented and their
applicable checks pass for the declared deployment. A conditional check may be marked not applicable only with the condition
and a verifiable reason that it is absent; this cannot waive an unconditional requirement. R-21 is additionally required
when efficiency or usefulness is claimed. Evidence may be absent, but its absence MUST be explicit; no independent-evidence
or convergence claim follows. R-03 applies to evidence when available and always forbids promoting trace into independent evidence.

**Full conformance:** minimal conformance plus independently enforced R-04 and the evaluation procedure in R-17.
An implementation can be conformant before a particular work item converges; it must not report convergence for that item
until the procedure passes. A local file pattern without enforced separation is an illustration, not structural conformance.

R-22 through R-24 govern additional claims about training use, demonstrated adoption or specification releases respectively.
An implementation must declare which are applicable and cannot claim an unevaluated extension.
Conformance reports identify version, deployment boundary, checks, results, limitations and applicable policies.

### 7.4 Current development state

This version has been adopted for the specification's own development memory, by one author on one machine.
That memory now resides in a separate private memory repository, bound to the specification's checkout by root commit
through a user-level registry; the public repository holds no memory and no pointer to it. The reference skills were changed
to resolve that binding, to write one record per file and to seal closed months. None of these changes demonstrates R-07 access enforcement, concurrent
writers, cross-machine identity or newcomer adoption, and the evidence statuses in Appendix A are unchanged by them.
No operational or full-conformance claim is made for this repository.

### 7.5 Implementation provenance

The `.acm/` pattern preceded this specification in the Principles of Earned Autonomy Skills Suite,
[DOI:10.5281/zenodo.19732827](https://doi.org/10.5281/zenodo.19732827), released April 24, 2026 (archived under its former name, the Autonomous Development Skills Suite).
Experience from using reference skills informs the living specification; earlier observations do not demonstrate the
new shared-memory contract or justify stronger novelty claims.

## 8. Shared Recording and Bounded Retrieval

### 8.1 Concurrent acceptance

**R-18.** Source recording MUST use the same collision-safe acceptance model for one writer and many. Every record MUST
have an identifier unique within the memory source (in the reference layout, the record's path in the memory repository, so the same time and slug in two scopes are two records). Acceptance MUST be atomic and create-only: a repeated identical
submission is idempotent; a repeated identifier with different content MUST fail explicitly, never replace or line-merge.
Concurrent distinct records MUST remain complete, attributable and individually retrievable.

A file-per-record layout is a reference strategy, not sufficient protection alone. A random identifier has a nonzero
collision probability; create-only acceptance detects collisions rather than assuming them impossible. Date/slug/author
alone is not a uniqueness guarantee. Publication of a local record is distinct from acceptance by the shared source.

A monolithic legacy trail may be retained read-only and referenced. A migration must account for every old record without
silently rewriting it. Ordinary writers do not concurrently regenerate and commit the same derived reading surface.

**R-25.** The number of source files in a scope MUST be bounded by the closed periods plus the current period's records, not by the total number
of records ever written. The bound holds whenever a record is accepted into the scope: earlier closed periods are sealed first, and a
scope that receives no more records may exceed it until its next acceptance, which may never come. The records of a closed period MUST
be sealable into one immutable file in the same scope, in one atomic change that adds the sealed file and removes exactly the
records it contains. Every record's content MUST be preserved byte for byte and be individually verifiable after sealing, and
a sealed period MUST NOT be sealed again. Concurrent sealing attempts for the same period MUST fail visibly for all but one.

Sealing is consolidation, not compression: it changes packaging, never content. The reference strategy seals a UTC month,
`records/<yyyy-MM>.md`, holding each record of that month in file-name order, each preceded by a marker line
`<!-- record: <file name> bytes:<n> sha256:<hash> -->` (the hash in lowercase hex) and followed by one LF that belongs to the sealed file, not to the
record. A record is exactly the `n` bytes after its marker line, so a marker-like line inside a record is content, and a
record without a final newline, with CRLF or with a byte-order mark round-trips unchanged. The removed files remain in version
history. A record's name carries its write time, not its acceptance time, so a record can arrive after its month is sealed,
or be retried after it was sealed. Acceptance therefore compares a submitted record with the sealed marker of the same identifier: the same SHA-256 is already
accepted, and different content under that identifier is a collision that fails explicitly (R-18). A record that belongs to a
sealed month is accepted only under a new identifier of the current time. Readers read `records/` in file-name order, sealed and
open files alike. Sealing needs only version control and a byte-count and SHA-256 tool: an agent following the reference skills,
or a person, can perform and verify it. The memory repository must store records byte for byte. Normalizing line endings in
`records/` makes the markers stop matching; for Git, mark them `-text`. A deployment where commits are not permitted does not meet the bound for closed periods until they are sealed, and reports that.

### 8.2 Derived views and freshness

**R-19.** Indexes, summaries, recent windows and orientations MUST be labelled derived and tied to the source revision or
snapshot they represent. Before use, validate that revision and coverage against the bound source. Stale or incomplete
views MUST be regenerated or reported unusable; filesystem, decoding and indexing errors MUST NOT silently omit scopes.
Deleting a derived view MUST NOT lose source decisions or governing intent.

Regenerate from a consistent accepted snapshot, including after synchronization. Do not hand-merge generated views.
Source changes during generation require a new snapshot or a visible retry/failure, not a falsely current result.
The governing mandates themselves are read at the revisions required by R-09, not replaced by summaries.

### 8.3 Relevance, compression and correction

**R-20.** Retrieval MUST provide a bounded route from binding and governing mandates to the applicable scope's current
orientation/index, then to relevant source and evidence references. A derived claim MUST identify its sources and author;
where it includes a corrected claim, it MUST expose the known correction/supersession reference. Recency MUST NOT by itself
promote a claim to authority.

Mechanical marker/date windows are a useful selection strategy. They do not guarantee preservation of meaning: test questions
whose answers depend on rejected alternatives, corrections and reasons. Escalate to source when the bounded view cannot
support an answer, and state uncertainty when relevant evidence is unavailable.

**R-21.** Deployments claiming efficient or useful accumulated memory MUST measure cold-start task orientation, context
cost and answer/traceability quality against a stated baseline and question set. Entry count and marker volume MUST NOT
substitute for value. The specification imposes no universal token budget or claim that compression is always beneficial.

### 8.4 Evaluation and training

**R-22.** Extraction for evaluation, training or tuning MUST have an explicit policy from the memory's principal defining
allowed purposes, scope and access. Training permission MUST NOT be inferred from permission to read or record.
Prefer independently captured observations and principal-confirmed outcomes where available; label trace-derived cases as
self-report. Evaluation before model changes is recommended, not an automatic entitlement to use private content.

## 9. Adoption and Specification Evolution

### 9.1 Adoption bar

**R-23.** A reference implementation claiming seamless adoption MUST demonstrate a newcomer continuing applicable work
by naming one installed entry skill after ordinary deployment setup, without reading the specification or manually supplying
memory paths in each prompt. The test MUST include successful scope selection and visible safe failure for unavailable
memory, not just installation or generation of an index.

Repeat with another contributor or agent, task and session. Measure time/context and the ability to find the reasons for
prior decisions. Record configuration assumptions. This specification states the bar; it does not claim it has been achieved.

### 9.2 Evidence-driven releases

**R-24.** Specification changes MUST preserve their versioned baseline, reasoning and evidence limitations. Before a release,
the specification version, citation version, archive metadata version and requested release tag MUST agree. A mechanical
check MUST reject drift before publication. An author-owned release decision is required in addition to passing checks.

Private observations remain private unless the author permits inclusion. Newly authored synthetic fixtures can demonstrate
bounded mechanisms without reproducing private content; they do not retroactively validate the private experiment.
Archive creation is a release-time decision. Historical public exposure remains an explicit issue, not fixed by an ignore
rule or by moving the current working copy.

## Appendix A: Requirement Checks

Each row gives a falsifiable implementation check. No row asserts that its check has been executed.

| ID | Evidence status | Protection | Check |
|----|-----------------|------------|-------|
| R-01 | Inherited | Classification | Present a derived summary as independent evidence; reject that classification and recover its source tier. |
| R-02 | Inherited | Confirmation boundary | Submit unconfirmed agent text and an unauthorized peer update; neither becomes governing intent. Confirmed updates have identifiable authority and revisions. |
| R-03 | Inherited | Conflict reporting | Seed unauthorized observed action and contradictory trace; preserve both, report the authorization violation and do not promote the trace. |
| R-04 | Inherited | Independent capture | Attempt agent filtering, alteration and deletion; reject each. Capture only after release (and, when streaming, after a portion was delivered); reject the ordering. Induce a capture failure and verify it is reported with the coverage gap. |
| R-05 | Inherited | Create-only source | Modify/delete an accepted trace record; reject it. Accept a new correction referencing that record; regenerating derived views leaves both intact. |
| R-06 | Proposed | Role separation | Exercise each write class under the wrong identity, including an unavailable acceptance service; fail visibly and preserve source. |
| R-07 | Proposed | Access and policy | A reader of the enclosing scope cannot retrieve a restricted scope without separate permission. Withhold each applicable recording, access and retention policy in turn: recording cannot begin. |
| R-08 | Proposed | Authorized exception | In a synthetic store, exercise authorized erasure across declared replicas and leave a non-sensitive event; an unauthorized writer cannot erase. Report unreachable replicas. |
| R-09 | Inherited | Pre-work gate | Missing mandate, unread governing revision or unaccepted interpretation prevents governed action. Agent-created text never authorizes itself. |
| R-10 | Proposed | Authority resolution | A lower scope cannot widen authority; conflicting peers and a mid-work mandate change pause affected actions until authorized resolution. |
| R-11 | Proposed | Explicit degraded state | Disconnect memory or trace acceptance; report it, pause dependent work and do not create fallback memory. Independent prompt-authorized work is not labelled ACM-valid. |
| R-12 | Proposed | Separate repository | The memory is held in a separately identified and governed store, two work repositories each resolve to their own repository-level scope in it, a clean distribution of the work repository contains no development memory, and adding a second writer requires no trace-layout migration. |
| R-13 | Proposed | Validated binding | Rename/move clones, use two same-named repositories, a fork sharing the root commit and conflicting bindings; with root-commit binding, a clone or fork sharing a root commit resolves to the same binding (another mechanism states its own fork rule), and the others preserve intended identity or fail, with no guessed reads/writes. |
| R-14 | Proposed | Intent-based scope | Resolve a deep path, task ambiguity and a code-path move; retain old references and write only to the explicitly selected applicable scope; a code folder named like a scope entry resolves to a distinct, reversible path. |
| R-15 | Proposed | Containment and completeness | Test parent escape, absolute paths, link redirects, an inaccessible parent and depth exhaustion; no escaped access or silent omission occurs. |
| R-16 | Proposed | Attributable records | Inspect repository, memory-wide and preflight records: scope/context, actor, authority reference, mandate revision, work/session identifier and time are present in ordinary records with their record identifier and verification references, only a preflight record may leave work identity and mandate revision unresolved, and unresolved fields grant no authority, later associations preserve the original, and copies do not imply independent evidence. |
| R-17 | Inherited | Independent evaluation | A nonempty queue, an empty queue alone, an independent report that contains an actionable finding, or author self-review cannot produce convergence. A bounded independent report identifies unchanged artifacts and untested bars. |
| R-18 | Proposed | Atomic concurrent acceptance | Concurrent distinct entries survive exactly once; identical retries are idempotent; deliberate identifier/content collisions fail without overwriting; readers never observe a partially accepted record. |
| R-19 | Proposed | Snapshot freshness | Change source during/after generation and inject unreadable files; stale/incomplete views cannot be reported current. Removing derived files loses no source. |
| R-20 | Proposed | Source-backed retrieval | Ask why a decision was reversed from a compressed view; reach the correction and original source when they exist, and report insufficient context without inventing an answer when they do not; the context needed to answer does not grow with unrelated history. |
| R-21 | Proposed | Measured usefulness | Compare a fixed cold-start question set with and without scoped views; report context cost, time, correctness and unresolved why-questions, not entry volume. |
| R-22 | Proposed | Purpose authorization | Read permission alone cannot start an extraction pipeline; an allowed dataset retains source-tier and policy provenance. |
| R-23 | Proposed | Cold-start adoption | A new contributor names one installed skill in a fresh session; with memory available it reaches the intended bound scope and continues a bounded task from prior memory, recovering the reason for an earlier decision, and with memory unavailable it fails explicitly, in both cases without per-prompt memory configuration. |
| R-24 | Proposed | Release consistency | A mismatch among specification/citation/archive versions or requested tag causes the pre-publication check to fail; passing it does not publish. |
| R-25 | Proposed | Bounded file count | Seal a closed month: one file replaces its records, each record's bytes and SHA-256 match the originals (including a record without a final newline, with CRLF or a byte-order mark, and one containing a marker-like line), and a second seal of that month or a concurrent seal fails visibly. A seal that adds the sealed file without removing its records, or the reverse, is rejected without changing the accepted records, and so is a seal whose bytes differ from the records it removes. Accept a record into a scope that still holds unsealed files of a closed month: sealing precedes acceptance. Submit a record of the sealed month late, retry an already sealed record and retry a sealed identifier with different content: the first is accepted only under a new identifier of the current time, the second as already accepted, the third fails as a collision. |

The rows state the main falsifying tests, not every obligation of a requirement; an implementation that passes them can still violate
one. Natural-language interpretation, policy quality and useful relevance are not made impossible to get wrong by a schema.
R-06/R-18 can enforce prohibited writes and collisions; R-20/R-21 require measured semantic outcomes and visible uncertainty.
Conformance claims must distinguish enforced properties from instructions that contributors are merely expected to follow.

## Appendix B: Evidence and Open-Problem Disposition

The development ledger is not part of this specification's distribution. Historical identifiers are mapped here for continuity.
The earlier profile proposals are source material, not a separate standard or an independent source of team conformance.

### B.1 Available evidence and its limits

- Published v0.4.0 defines the inherited governance properties; publication alone is not enforcement evidence.
- Private replay experiments observed shared-file merge conflicts and some silent line loss with line-union merging.
  Distinct-file cases preserved the tested records. Colliding identifiers, authenticated provenance, hosted acceptance,
  real simultaneous contributors and reader cost were not established. R-18 remains Proposed.
- A private index/resolution prototype found stale manual counts and resolved the tested paths. Remote basenames, local
  fallbacks, incomplete boundary handling and error-handling limitations prevent promoting it into the validated binding
  required here. R-13/R-15/R-19 remain Proposed.
- This specification contains no private source records or new runtime experiment results. New synthetic fixtures and independent
  evaluation remain work to do. Descriptions above summarize limitations, not permission to publish the private material.

### B.2 Disposition

Requirement coverage means the design addresses the topic; it does not close its implementation evidence gap.

| ID | Disposition | Remaining evidence or reason to reopen |
|----|-------------------|---------------------------------------|
| BS-01 | Proposed: R-19/R-20/R-21 | Measure staleness and answer quality at growing memory sizes. |
| BS-02 | Proposed: R-19/R-20/R-21 | Test compressed why-questions and correction/source reachability. |
| BS-03 | Out of generic specification | Adopting principals own content/licensing decisions; no organization-specific claim here. |
| BS-04 | Proposed: R-07/R-08 | Adopter policy and end-to-end synthetic erasure, including inaccessible replicas. |
| BS-05 | Proposed: R-06/R-07 | Test access separation and permitted recording; content sensitivity is not decidable by schema alone. |
| BS-06 | Proposed: R-02/R-03/R-20 | Seed a wrong claim and verify it cannot acquire mandate authority; evaluate downstream correction. |
| BS-07 | Proposed: R-02/R-10 | Exercise collective decision rules, peer conflicts and owner succession without self-authorization. |
| BS-08 | Proposed: R-05/R-16/R-18/R-25 | Test ID collisions, retries, authenticated attribution and real concurrent acceptance. |
| BS-09 | Proposed: R-22 | Adoption-specific purpose policy and an authorized evaluation dataset; no universal training permission. |
| BS-10 | Proposed: R-19/R-20/R-21 | Measure cold-start retrieval cost and correctness; verify source revision and indexing completeness. |
| BS-11 | Proposed: R-21/R-23 | Independent newcomer comparison with a baseline; volume is not value. |
| BS-12 | Deferred: broader contemporary prior-art survey | Release/novelty claims beyond the modest positioning require first-hand verified sources. |
| BS-13 | Superseded: no separate profile | R-24 pins releases and preserves history; future compatibility changes need explicit migration. |
| BS-14 | Inherited lesson, proposed release process: R-24 | New requirements remain Proposed until their own evidence exists; keep visible reversals. |
| BS-15 | Proposed: R-11/R-13/R-14/R-15 | Implement the reference binding contract and test clone/worktree/identity/boundary cases. |
| BS-16 | Proposed: R-23 | Cold-run tests through the reference skill in its separately governed repository. |

Other deferred matters: author permission for private evidence publication; archive record creation; legal sufficiency of
retention procedures; external review and academic submission. These do not prevent publishing this specification. They prevent
claims that their own obligations or relevant release bars have been satisfied.

## Appendix C: Historical Reference Mapping and Release Boundary

The published v0.4.0 specification numbers both Convergence and Prior Art as Section 5. This version keeps Convergence at 5,
renumbers Prior Art to 6 and Reference Implementation to 7. Historical documents are not rewritten.

| Historical v0.4.0 reference | v0.5.0 reference |
|--------------------------------|------------------------|
| Sections 1-3 | Sections 1-3; requirements now have stable R-identifiers |
| Section 4, Scoped Memory | Section 4, Memory Repository and Scopes |
| Section 5, Convergence (5.1-5.8) | Section 5, Convergence |
| Section 5, Prior Art (5.1-5.7) | Section 6, Prior Art and Contribution (6.1-6.7) |
| Prior Art 5.5, The Governance Gap (its paragraph on scale within the trace tier) | Sections 6.5 and 8 |
| Prior Art 5.6, contribution claims | Section 6.6 |
| Section 6, Reference Implementation (6.1-6.6) | Section 7, Reference Implementation and Conformance |
| Appendices A/B, PEA relationship and glossary | Sections 1/5.2 and Appendix D |

Older erroneous references to "Section 4" for Convergence are interpreted by the named topic, with this mapping made explicit,
not by changing source history. Old prototype and profile links are not normative dependencies of this specification.

Publication must separately reconcile README, citation/archive descriptions, release workflow, requested tag and version. The
repository's release workflow rejects a tag that differs from the versions stated in `SPEC.md`, `CITATION.cff` and `.zenodo.json`,
and rejects a tag that carries development memory (`.acm/` or `.trail/`) at the repository root. Release packaging must exclude private development
memory by an explicit distribution boundary. Passing a version/structure check does not prove the absence of sensitive content
from a public release.

## Appendix D: Glossary

- **Principal:** the party with standing and an explicit decision rule to authorize a scope's work.
- **Mandate:** principal-confirmed what, why and constraints; proposed agent articulation is not yet a mandate.
- **Governed interval:** work after resolved mandates and a visible accepted interpretation pass the mandate gate.
- **Memory repository:** the separately identified and governed store holding memory for repositories of work.
- **Binding:** explicit, validated association of work identity, memory identity and repository-level scope.
- **Scope:** a declared authority/relevance boundary within memory; not an access permission.
- **Source record:** accepted intent, trace or captured evidence retained under its tier's authorship/mutation rules.
- **Derived view:** replaceable index, window, summary or orientation with source provenance and snapshot identity.
- **Acceptance boundary:** the mechanism that admits records and enforces roles, immutability and collision handling.
- **Capture-author separation:** the observed agent cannot author or suppress its independent observation record.
- **Silence:** a bounded, provisional independent evaluation with no findings against the examined quality bar.
- **Convergence:** scoped work queue exhaustion plus bounded independent evaluation, not an agent declaration.

*End of specification.*
