# Shared Agent Context Memory (SACM) Specification

**Version:** 0.7.0
**Date:** 2026-11-22
**Author:** Nils Wendelboe Holmager
**License:** CC BY-SA 4.0
**Status:** Working draft; not an implementation-conformance claim. Every requirement is Proposed: none has been exercised by anyone but its author.
**Baseline:** ACM v0.4.0 (the name this specification had before SACM; commit 51e951e of this repository; never tagged).

## Abstract

Shared Agent Context Memory (SACM) specifies how AI-assisted work is kept organised in one shared memory, so that humans can
audit it and stay responsible for the result. Its purpose is cumulative, trustworthy work across people, sessions, tools and
models, while the principal retains authority: an agent starts from what earlier work learned, found and was corrected on,
rather than starting over. The same model serves one contributor and many; a person working alone is a team of one, and only
the number of people and agents reading and writing changes.

SACM transfers established authorization and audit-log patterns to agent memory governance. Its mandate gate requires
principal-confirmed intent before governed work. Three trust tiers distinguish authorization, agent self-report and
independently captured evidence. Scope identifies what the memory governs; access controls identify who can use it.

Memory resides in its own repository, separately from repositories of work.
Binding connects work to that memory; scoped retrieval makes it usable without reading the entire accumulated history.
The specification defines required properties, not a mandatory vendor, configuration format, storage engine or skill host.

SACM implements the [Principles of Earned Autonomy (PEA)](https://github.com/ntholm86/principles-of-earned-autonomy), which are its
one dependency. It can be implemented without any particular skills or tools.

## What the shared model is for (informative)

Intended outcomes, not demonstrated results (section 7.4): learning that accumulates across people; continuity across sessions,
tools and models; earned autonomy at team scale (a shared, auditable record lets a team decide how much to delegate, and an agent
working from the team's accumulated context is safer to delegate to); orientation for a newcomer; attributable records usable as
audit evidence; and memory the principal owns (use for evaluation or training is the owner's decision, R-21). The model does not
change when more people write; what must be decided is who the principal is (R-02), the recording and access policy (R-07) and
how peer conflicts are resolved (R-10).

## Reading and requirement status

MUST, MUST NOT, SHOULD and MAY express conformance requirements; each has a stable identifier and a check in Appendix A.
Every requirement is Proposed (see Status above). The checks are specified tests, not executed results; a structure or version
check does not establish SACM conformance, privacy, useful retrieval or correct agent behaviour.

Terms: the *mandate* is the intent tier: a confirmed `mission.md` (older memories call it `destination.md`) or, for a run that has none, the interpretation of the prompt that the principal confirmed for that run (or delegated in the prompt or a mission), stated in the run's checkpoint or single record; *records* and the derived `learning.md` are the trace tier (*trail* means the same, and `audit-trail.md` is the legacy single-file form); `evidence/` is the evidence tier.

## 1. Trust Tiers

### 1.1 Intent, trace and evidence

SACM organizes memory by authorship and trust, independently of functional memory types.

| Tier | Responsibility | Authority and mutability |
|------|----------------|--------------------------|
| Intent | Principal-confirmed mandate: what, why and constraints | Governs authorization; changes require principal confirmation |
| Trace | Attributable agent decisions, interpretations, corrections and outcomes | Source records are append-only; derived views are replaceable |
| Evidence | Independently captured execution observations | Agent cannot author, filter, alter or delete the capture |

**R-01.** An implementation MUST identify each artifact's tier and whether it is source or derivation. A summary, index or
learning file does not gain independent evidentiary standing merely by being stored beside source records.

### 1.2 Mandate ownership and articulation

**R-02.** Each governing scope MUST identify its principal (by default the session operator) and the mechanism by which that principal confirms a mandate or
delegates authority. A principal may be a person, role or collective with an explicit decision rule. Agent-drafted candidate
text MUST NOT become intent without that confirmation. The principal owns the meaning, not necessarily all the typing.
Confirmation and mandate revisions MUST be identifiable to later readers: a confirmation MUST be recorded with its source (the principal's own commit, a host approval, or the principal's reply in the session) and never inferred from silence, and a mandate revision is identified by the hash of its content. A mandate MUST state where the human gate is (who
reviews the work before it takes effect, or that no one does) and what the agent may do before that gate and without it; for a run mandate, the confirmed interpretation names the gate and the principal's Proceed or delegation says what may happen before it.

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

Useful skills may be distilled from accumulated learning. That is an awareness, not an SACM skill-generation function.
SACM does not prescribe where skills live or how a principal adopts them.

## 2. Structural Requirements

### 2.1 Capture-author separation

Independent capture is warranted where work takes effect without a human gate: no person or accountable system reviews the
result, and can refuse it, before it takes effect (for example autonomous action on live data). Where such a gate exists, the
reviewed trace and the reviewer's decision are what the record rests on, and an evidence tier is not required. They remain
the agent's account and the reviewer's decision, not independent evidence (R-03); a reviewer who wants more than the account
looks at the result itself.

Behind such a gate an implementation may run without independent capture, for example because no capture component works with
its host. That is acceptable when the absence is declared, not hidden (the conformance report says so, section 7.3), and the
person at the gate remains responsible for the result. Work that takes effect without such a gate is the case in which capture is
required (R-04).

**R-04.** Where a mandate authorizes work without a human gate, requires an evidence tier, or one is claimed, a component architecturally separate from the observed agent MUST capture the
declared interactions before releasing their responses to that agent. The agent MUST NOT be able to suppress or edit that
capture. Coverage, gaps and capture failures MUST be observable. Reconstructed self-report is not independent evidence.

For response streaming, capture must precede delivery of each captured portion. A full-evidence claim MUST identify any
unobserved tools, channels or operations rather than implying comprehensive execution coverage.

### 2.2 Append-only source, replaceable derivations

**R-05.** Accepted trace source records MUST be immutable to ordinary contributors: addition is permitted, replacement and
deletion are not. A correction MUST create a new record referring to the corrected identifier. Derived views MAY be
replaced, but MUST identify their source and MUST NOT be the only surviving copy of a decision.

Git history and an instruction saying "do not edit" are not alone enforcement: a later commit can change an old file.
An implementation claiming structural protection must reject such changes at an acceptance boundary the agent cannot bypass; otherwise
such changes are detected (section 2.5). Record any
administrative override as a governance event. Chronological presentation does not prove causal order between concurrent writers.

### 2.3 Roles and permissions

**R-06.** An implementation MUST keep mandate confirmation and trace addition in separate roles, and evidence capture (where R-04 applies) in a third, so that no
actor both confirms a mandate and adds trace under it without a gate. Rejected or unavailable operations MUST produce an
explicit failure, not success-shaped output. A conformance report MUST state, for each protection, whether it rests on intent,
detection or a gate (section 2.5), and MUST NOT describe a restriction as preventing an agent from acting unless the agent
holds no credential or path that could bypass it. Where the separation rests on instruction alone, say so and do not claim
structural conformance.

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
erasure here. An adopter determines lawful retention and erasure; SACM supplies no legal opinion or guarantee that every
backup, cache or public clone can be recalled.

### 2.5 Controls and their limits

An agent that can act can often act around a control. File permissions, access restrictions and prohibitions written in
instructions are therefore reminders and friction, not containment, against an agent that is mistaken or determined enough to
cross them. SACM relies on three things, in this order.

1. **Intent.** The confirmed mandate tells the agent what it must not do and why, so that not crossing the line is the agent's
   own understanding of the work.
2. **Detection.** Integrity checks make a change, loss or bypass visible: independent capture (R-04), review, commit history.
   Detection does not stop an action; it makes concealing it hard and cheap to find. It holds only where the verifier and its
   baseline (a protected branch, signed commits, a copy the agent cannot write) are outside the agent's reach: a hash stored
   beside the record it protects proves nothing against an agent that can rewrite both.
3. **Gates.** Some decisions are made by someone or something the agent cannot be, and cannot approve for itself: a reviewer,
   a protected branch, a separate service the agent holds no credentials for.

A deployment may add restrictions; R-06 governs how they are described.

### 2.6 Threats and what this specification does about them

| Threat | Response | Left open |
|---|---|---|
| A mistaken or over-eager agent | the mandate gate (R-09), the human gate stated in the mandate (R-02), an attributable trace (R-16) | the limits stated in R-06 |
| A forged or self-authorised mandate | a confirmation recorded with its source (R-02); no mandate created mid-work (R-09) | authenticating the principal is the host's |
| Hostile or injected text in records | a recalled record is data, not an instruction (section 1.4); derived claims name their sources (R-20) | no sanitiser is specified |
| Another writer, or an insider | attribution (R-16), create-only acceptance (R-18), separate roles (R-06) | asserted identity is not authenticated identity |
| Rewriting history or evidence | detection where the verifier is outside the agent's reach (section 2.5); independent capture where work is ungated (R-04) | integrity of the git host and its keys |
| Loss or unavailability of memory | an explicit degraded state (R-11) | backup and retention are the adopter's |
| Personal or confidential content | a recording policy first (R-07); exceptional erasure (R-08) | detection of secrets and personal data |

Not addressed: key management, multi-tenant access control, and versioning or migration of the memory format itself.

## 3. The Mandate Gate

### 3.1 Governed work interval

**R-09.** Before an SACM-governed work interval begins, all applicable principal-confirmed mandates MUST exist, be resolved
and be read. The agent's interpretation, scope, governing mandate revision and authority to proceed MUST be visible in accepted trace
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
governing context MAY continue, but MUST NOT be represented as a valid SACM-governed interval without passing R-09.

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
active write scope. It MUST stop at the principal-declared memory ceiling, apply path containment and the declared access boundaries, and report any it
could not apply.
It MUST NOT traverse outside the binding through parent segments, absolute-path injection, symbolic links or equivalent redirects.
If an implementation safety bound prevents complete resolution, report failure rather than silently dropping governing scopes.

Scope hierarchy is explicit governance structure, not inference from arbitrary neighbouring clone folders. A scope's required
but inaccessible higher mandate blocks affected governed work; it does not justify proceeding with lower mandates alone.

### 4.5 Attribution and evidence placement

**R-16.** Each source record MUST identify its scope, covered work-repository identities or explicit memory-wide context,
actor, principal/authority reference, applicable
mandate revision (the hash of the governing mandate's content; none where only a run's confirmed interpretation governs), work/session identifier, record identifier, time and relevant verification references. Agent/model
identity MUST be stated where known; otherwise state unknown, not an invented model name.

Record asserted identity separately from authenticated identity. A Git author field alone is not authentication.
For preflight observations before work identity or mandate revisions are established, mark those fields unresolved and retain
the capture's memory/session identity. Mark genuinely inapplicable fields explicitly. Neither case authorizes governed work;
later association with the validated work context is recorded without rewriting the original observation.
Evidence is associated with the scope of the documented actions. A cross-scope session may have one canonical capture with
scope-specific references, rather than copied captures that imply separate observations.

## 5. Convergence

**R-17.** Convergence MUST require both an exhausted work queue for the declared scope and independent evaluation finding
nothing further to change against its declared quality bar. The agent's self-assessment or a passing automated check alone
MUST NOT establish convergence.

Evaluation identifies the artifact revision/hash, examined surfaces, untested surfaces, evaluator identity and independence,
and whether the evaluation is fresh or a same-session follow-up. A different model family reduces one source of shared bias;
it is not proof of statistical independence. Principal review has role independence, not necessarily organizational independence.
Independent evaluation follows Principle 3 of PEA and its minimum bar (PRINCIPLES.md): fresh sessions, distinct model families, an unchanged artifact. An evaluation that
is not so (a same-session follow-up, a principal review) is recorded as what it is and does not alone establish convergence.

Silence is bounded, provisional evidence of no findings, not proof that none exist. Deferred items outside an evaluated
scope remain visible; deferring an unresolved item inside the claimed scope is not the same as completing it.

## 6. Prior Art and Contribution

SACM builds on three capability-first memory models. Sumers et al. (2023), *Cognitive Architectures for Language Agents*
([arXiv:2309.02427](https://arxiv.org/abs/2309.02427)), distinguish functional memory types; Park et al. (2023), *Generative
Agents* ([arXiv:2304.03442](https://arxiv.org/abs/2304.03442)), supply the observation, reflection and planning precedent;
Packer et al. (2023), *MemGPT* ([arXiv:2310.08560](https://arxiv.org/abs/2310.08560)), supply hierarchical memory and
cross-session persistence. SACM keeps those capabilities and adds governance: authorship, authority and verifiability, and
reflection as a derivation distinct from source evidence and confirmed intent. Role-based access control (Sandhu et al., 1996),
MAC and capability models establish authorization before action and separation of actor from authority; independent capture
and append-only source apply established audit-log patterns. These analogies motivate SACM; they do not prove that
natural-language mandates are machine-enforceable or that SACM replaces per-operation access checks.

SACM's stated contribution is transferring established authorization patterns to agent memory governance: the mandate gate,
capture-author separation, trust-tier organization and convergence as a memory-state property. It claims no invention of shared
memory, tiered memory, reflection, persistence or the underlying authorization mechanisms, and observed results are claimed only
within their stated scope. Freshness of derived views needs a validated source revision and a completeness check (R-19);
generation alone does not prevent stale caches.

## 7. Reference Implementation and Conformance

### 7.1 Separate memory, ordinary entry point

An implementation is used by a person naming it in an ordinary prompt; setup has already supplied the binding. It resolves the
scope, reads governing intent and bounded relevant context, makes its interpretation visible, acts within authority and records
outcomes to the bound scope. Naming an implementation cannot compensate for absent deployment configuration or permission
controls.

This specification implements the PEA principles and stands on its own; it names no implementation. An implementation follows
the specification for how memory is kept and the principles for how it works, and states which requirements it does not meet (for
example independent evidence capture needs a separate component, R-04, and the recording policy is the deployment's, R-07). It
may read, and append to, an in-repository `.acm/` from an earlier version as a transitional legacy mode; that is not the separate
memory repository of R-12.

This is a contract for implementations, not a claim that any implementation meets it.

### 7.2 File layout

The following layout is illustrative. A database or service may implement the same properties.

```text
memory-repository/            memory-wide scope; .acm-root marks the top
  .acm-root
  mission.md                  intent: the mandate (principal-confirmed)
  learning.md                 trace: what this scope has learned so far; derived and replaceable
  records/<time>-<slug>.md      trace source: records, one file each, identified by their path (R-18)
  tasks/<task-id>/            named task scope; the same three entries (a task may sit under any scope)
  generated/                      optional, disposable reading aids (any scope); never required reading
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
summaries, recent windows) are not required by the layout: they are regenerated on demand from the records (R-19). An
implementation that stores them keeps them together in an optional `generated/` folder in the scope, each marked as generated,
never required reading and not part of the memory's source. The one stored derived view that a scope normally carries is
`learning.md`, labelled derived (R-19).

The governing chain is the folder nesting: memory-wide, repository, path, task. A scope's mandate narrows its parent's and never contradicts it. A task that works inside one path lives under that path; a task that spans paths lives at repository level and names those paths in its own `mission.md`; a task not tied to one
repository lives at memory-wide level. A task at repository level also reads the mandates of the paths it names, as peers: a conflict between them pauses the affected action until authorized resolution (R-10). A code folder named like one of a scope's own entries (`records`, `tasks`, `evidence`, `generated`, or any name ending in `.md`), or beginning with `_` (names compared without regard to letter case), is written under `paths/` with one extra leading `_` and its original spelling, so a scope's entries and its child paths never share a name and the mapping stays reversible. The binding maps repository identity to its namespace.
Labels such as `<repository-id>` are not instructions to derive identity from a folder basename.
An implementation can place restricted scopes in separate repositories with independent access controls.

### 7.3 Conformance

The contributor count is not a conformance mode. A deployment conforms to this version when every requirement that applies to it is implemented and its check passes for the declared deployment: R-01 to R-03, R-05 to R-16 and R-18 to R-20 always; R-04 where a mandate authorizes work without a human gate (a deployment whose work always passes one says so instead); R-17 when it claims convergence; R-21 when it extracts memory for training or evaluation. A requirement may be marked not applicable only with its condition and a verifiable reason; this cannot waive an unconditional requirement. R-03 always forbids promoting trace into independent evidence, and a deployment without an evidence tier makes no independent-evidence or convergence claim. A local file pattern without separate roles is an illustration, not conformance. An implementation can conform before a particular work item converges; it must not report convergence for that item until R-17 passes.

A conformance report states the version, the deployment boundary, the checks, the results, the limitations and the policies that apply.

### 7.4 Current development state

This version has been adopted for the specification's own development memory, by one author on one machine, in a separate
private memory repository bound by root commit; the public repository holds no memory and no pointer to it. That demonstrates
none of R-07 access control, concurrent writers, cross-machine identity or newcomer adoption. The development runs behind a
human pull-request gate and without independent capture (section 2.1). No deployment yet runs an autonomous agent on a shared
memory without such a gate; that would require independent capture (R-04). No operational or full-conformance claim is made
for this repository.

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

The number of files in a scope is a storage concern of the host; this specification does not bound it.

### 8.2 Derived views and freshness

**R-19.** Indexes, summaries, recent windows and learning files MUST be labelled derived and tied to the source revision or
snapshot they represent. Before use, validate that revision and coverage against the bound source. Stale or incomplete
views MUST be regenerated or reported unusable; filesystem, decoding and indexing errors MUST NOT silently omit scopes.
Deleting a derived view MUST NOT lose source decisions or governing intent.

Regenerate from a consistent accepted snapshot, including after synchronization. Do not hand-merge generated views.
Source changes during generation require a new snapshot or a visible retry/failure, not a falsely current result.
The governing mandates themselves are read at the revisions required by R-09, not replaced by summaries.

### 8.3 Relevance, compression and correction

**R-20.** Retrieval MUST provide a bounded route from binding and governing mandates to the applicable scope's current
learning/index, then to relevant source and evidence references. A derived claim MUST identify its sources and author (for a derived view, the source revision it is tied to under R-19 and who wrote it);
where it includes a corrected claim, it MUST expose the known correction/supersession reference. Recency MUST NOT by itself
promote a claim to authority.

Mechanical marker/date windows are a useful selection strategy. They do not guarantee preservation of meaning: test questions
whose answers depend on rejected alternatives, corrections and reasons. Escalate to source when the bounded view cannot
support an answer, and state uncertainty when relevant evidence is unavailable.

### 8.4 Evaluation and training

**R-21.** Extraction for evaluation, training or tuning MUST have an explicit policy from the memory's principal defining
allowed purposes, scope and access. Training permission MUST NOT be inferred from permission to read or record.
Prefer independently captured observations and principal-confirmed outcomes where available; label trace-derived cases as
self-report. Evaluation before model changes is recommended, not an automatic entitlement to use private content.

## Appendix A: Requirement Checks

Each row gives a falsifiable implementation check. No row asserts that its check has been executed.

| ID | Protection | Check |
|----|------------|-------|
| R-01 | Classification | Present a derived summary as independent evidence; reject that classification and recover its source tier. |
| R-02 | Confirmation boundary | Submit unconfirmed agent text and an unauthorized peer update; neither becomes governing intent. Confirmed updates have identifiable authority and revisions. A confirmation with no recorded source, including an agent's bare claim that the principal confirmed, is not confirmed. A mandate that does not state its human gate is incomplete. |
| R-03 | Conflict reporting | Seed unauthorized observed action and contradictory trace; preserve both, report the authorization violation and do not promote the trace. |
| R-04 | Independent capture | Present a mandate that authorizes work without a human gate and requires no evidence tier; reject it. Attempt agent filtering, alteration and deletion; reject each. Capture only after release (and, when streaming, after a portion was delivered); reject the ordering. Induce a capture failure and verify it is reported with the coverage gap. |
| R-05 | Create-only source | Modify or delete an accepted record: it is rejected at an acceptance boundary the agent cannot bypass, or detected and reported (the report says which; a deployment with neither says so, and none of the reference tools detects it yet). Accept a new correction referencing that record; regenerating derived views leaves both intact. |
| R-06 | Role separation | Exercise each write class under the wrong identity, including an unavailable acceptance service; fail visibly and preserve source. The report names, for each protection, intent, detection or gate, and claims no prevention where the agent holds a credential or path that bypasses it. |
| R-07 | Access and policy | Retrieval of a restricted scope outside its permission is either prevented by a control the agent holds no credential to bypass, or is detected and reviewable; the report says which. Withhold each applicable recording, access and retention policy in turn: recording cannot begin. |
| R-08 | Authorized exception | In a synthetic store, exercise authorized erasure across declared replicas and leave a non-sensitive event; an unauthorized writer cannot erase. Report unreachable replicas. |
| R-09 | Pre-work gate | Missing mandate, unread governing revision or unaccepted interpretation prevents governed action. Agent-created text never authorizes itself. |
| R-10 | Authority resolution | A lower scope cannot widen authority; conflicting peers and a mid-work mandate change pause affected actions until authorized resolution. |
| R-11 | Explicit degraded state | Disconnect memory or trace acceptance; report it, pause dependent work and do not create fallback memory. Independent prompt-authorized work is not labelled SACM-valid. |
| R-12 | Separate repository | The memory is held in a separately identified and governed store, two work repositories each resolve to their own repository-level scope in it, a clean distribution of the work repository contains no development memory, and adding a second writer requires no trace-layout migration. |
| R-13 | Validated binding | Rename/move clones, use two same-named repositories, a fork sharing the root commit and conflicting bindings; with root-commit binding, a clone or fork sharing a root commit resolves to the same binding (another mechanism states its own fork rule), and the others preserve intended identity or fail, with no guessed reads/writes. |
| R-14 | Intent-based scope | Resolve a deep path, task ambiguity and a code-path move; retain old references and write only to the narrowest applicable scope (a task the principal named, else the path scope containing the work); a code folder named like a scope entry resolves to a distinct, reversible path. |
| R-15 | Containment and completeness | Test parent escape, absolute paths, link redirects, an inaccessible parent and depth exhaustion; no escaped access or silent omission occurs. |
| R-16 | Attributable records | Inspect repository, memory-wide and preflight records: scope/context, actor, authority reference, mandate revision, work/session identifier and time are present in ordinary records with their record identifier and verification references, only a preflight record may leave work identity and mandate revision unresolved, and unresolved fields grant no authority, later associations preserve the original, and copies do not imply independent evidence. |
| R-17 | Independent evaluation | A nonempty queue, an empty queue alone, an independent report that contains an actionable finding, or author self-review cannot produce convergence. A bounded independent report identifies unchanged artifacts and untested bars. |
| R-18 | Atomic concurrent acceptance | Concurrent distinct entries survive exactly once; identical retries are idempotent; deliberate identifier/content collisions fail without overwriting; readers never observe a partially accepted record. |
| R-19 | Snapshot freshness | Change source during/after generation and inject unreadable files; stale/incomplete views cannot be reported current. Removing derived files loses no source. |
| R-20 | Source-backed retrieval | Ask why a decision was reversed from a compressed view; reach the correction and original source when they exist, and report insufficient context without inventing an answer when they do not; the context needed to answer does not grow with unrelated history. |
| R-21 | Purpose authorization | Read permission alone cannot start an extraction pipeline; an allowed dataset retains source-tier and policy provenance. |

The rows state the main falsifying tests, not every obligation of a requirement; an implementation that passes them can still violate
one. Natural-language interpretation, policy quality and useful relevance are not made impossible to get wrong by a schema.
R-06/R-18 can reject prohibited writes and collisions at an acceptance boundary or detect them afterward; R-20 requires measured semantic outcomes and visible uncertainty.
Conformance claims must distinguish properties a control, check or gate provides from instructions that contributors are merely expected to follow.

## Appendix B: Glossary

- **Principal:** the party with standing and an explicit decision rule to authorize a scope's work.
- **Mandate:** principal-confirmed what, why and constraints: a confirmed mission, or the interpretation of a prompt the principal confirmed for one run; proposed agent articulation is not yet a mandate.
- **Governed interval:** work after resolved mandates and a visible accepted interpretation pass the mandate gate.
- **Memory repository:** the separately identified and governed store holding memory for repositories of work.
- **Binding:** explicit, validated association of work identity, memory identity and repository-level scope.
- **Scope:** a declared authority/relevance boundary within memory; not an access permission.
- **Source record:** accepted intent, trace or captured evidence retained under its tier's authorship/mutation rules.
- **Derived view:** replaceable index, window, summary or learning file with source provenance and snapshot identity.
- **Learning:** what a scope has learned so far, written as a derived view (`learning.md`) from its mandate and records; unrelated to training a model (R-21).
- **Acceptance boundary:** the mechanism that admits records and applies roles, immutability and collision handling (in the reference route, the create-only write of the record file; committing and pushing it is publication).
- **Capture-author separation:** the observed agent cannot author or suppress its independent observation record.
- **Silence:** a bounded, provisional independent evaluation with no findings against the examined quality bar.
- **Convergence:** scoped work queue exhaustion plus bounded independent evaluation, not an agent declaration.

*End of specification.*
