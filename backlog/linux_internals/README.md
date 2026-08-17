# Linux Internals

## Status

Backlog

## Objective

Provide an optional support path for security investigations that require
deeper operating-system reasoning. The learner activates only the relevant
phase, observes the required Linux mechanism, explains the boundary, and then
returns to the original research track.

## Scope

This roadmap covers Linux mechanisms that materially support an active
security question:

* process execution and kernel interfaces;
* descriptors, filesystems, memory maps, libraries, signals, and threads;
* identities, capabilities, namespaces, cgroups, seccomp, and security modules;
* the Linux network-stack packet path;
* small source examples used only to make an otherwise hidden mechanism
  observable.

## Non-Goals

Linux Internals is not a general systems-programming curriculum and is not a
fixed prerequisite for Web Security, Bug Bounty Operations, Wired Network
Security, or Wireless Security.

Do not create curricula for:

* C grammar;
* systems programming;
* assembly;
* ELF;
* exploit development;
* memory-corruption exploitation.

Tools such as `strace`, `lsof`, debuggers, and tracing frameworks are
instruments for answering a mechanism question, not separate roadmaps.

### C Usage Boundary

C may be used only when it materially improves observation of:

* a system call;
* descriptor inheritance;
* signals;
* memory mapping;
* a small Linux source-code example.

## Learning Method

Begin with a bounded question from an active investigation and follow the
evidence cycle:

```text
Scope
↓
Question
↓
Prediction or Hypothesis
↓
Observation or Controlled Test
↓
Evidence
↓
Explanation
↓
Record
```

Prefer an existing program and observable runtime state over writing new code.
Use the smallest controlled probe that distinguishes competing explanations.
Never record an inferred or expected result as observed fact.

## AI Assistance Boundary

Under [AI-Assisted Learning and
Research](../../ops/docs/AI_ASSISTED_LEARNING_AND_RESEARCH.md), AI may assist
source-tree navigation, explain system calls and data structures, generate
candidate call paths, compare documentation with source, design small
observation programs, and review `strace`, `/proc`, and debugger
interpretations.

Verify material claims directly through current source definitions, runtime
output, `/proc`, `strace`, `lsof`, debugger output, or controlled local
experiments. A plausible source explanation is insufficient when the relevant
version, configuration, or runtime state has not been checked.

## Lab Environment

Use a local Linux host, disposable VM, container, or network namespace whose
state the learner is authorized to inspect. Experiments should be reproducible,
reversible, and limited to the mechanism under study. Capture process, kernel,
filesystem, network, and log evidence only as needed to answer the central
question.

Public records may contain sanitized local-lab evidence and reusable mechanism
notes. They must not contain secrets, personal account data, private-program
evidence, confidential source, undisclosed vulnerability details, unsafe
real-target identifiers, or unredacted packet captures. Sensitive evidence
belongs in a separate private workspace.

## Prerequisites

There is no fixed roadmap prerequisite. Activate a phase when an active
security investigation reaches an operating-system mechanism boundary that
cannot be explained with the current evidence. Basic observation skills from
Security Core are normally sufficient to identify that boundary; this roadmap
deepens those skills without duplicating their canonical foundation.

## Phase Structure

The roadmap contains two independently activatable support phases. Each phase
moves from observable runtime state to mechanism boundaries and ends with a
capstone. A capstone requires evidence collection, reconstruction of state or
a boundary, alternative-explanation checks, an explanation, and explicit
limitations and uncertainty.

Phase and session purposes remain stable while fixtures and tools may adapt to
the investigation and local environment. A session normally has one central
question, no more than five core tasks, and one explicit completion criterion.

## Phases and Sessions

### Phase 01 — Runtime Internals

Goal: explain a running program by connecting user-space state to kernel
interfaces and observable runtime evidence.

1. **P01-S01 — User Space, Kernel Space and System Calls**
2. **P01-S02 — Process Creation and Replacement**
3. **P01-S03 — File Descriptors and Open File Descriptions**
4. **P01-S04 — VFS, procfs and sysfs**
5. **P01-S05 — Virtual Memory and Process Maps**
6. **P01-S06 — Shared Libraries and the Dynamic Loader**
7. **P01-S07 — Signals, Threads and Process State**
8. **P01-S08 — Capstone — Explain a Running Program from Process to Kernel Interface**

**Phase completion criterion:** The learner can explain one running program by
correlating process lineage, descriptors, filesystem state, memory maps,
loader state, signals or threads, and observed system calls with the relevant
kernel interfaces and evidence limits.

### Phase 02 — Isolation and Security Boundaries

Goal: reconstruct how Linux identity, isolation, policy, resource, and packet
path mechanisms constrain an authorized service.

1. **P02-S01 — Real, Effective and Saved Identities**
2. **P02-S02 — Linux Capabilities**
3. **P02-S03 — Namespaces**
4. **P02-S04 — cgroups**
5. **P02-S05 — seccomp**
6. **P02-S06 — Linux Security Modules**
7. **P02-S07 — Linux Network-Stack Packet Path**
8. **P02-S08 — Capstone — Analyze an Isolated Service Boundary**

**Phase completion criterion:** The learner can reconstruct one isolated
service boundary by correlating identity, capabilities, namespaces, resource
controls, syscall policy, security-module policy, and packet-path evidence,
then reject a competing explanation and state remaining uncertainty.

## Completion Criteria

A phase is complete when the learner can:

* correlate user-space observations with the relevant kernel interface;
* reconstruct the runtime or isolation boundary from multiple evidence sources;
* distinguish identity, policy, namespace, resource, and network constraints;
* reject alternative explanations with a controlled observation;
* explain what the evidence establishes and what remains uncertain;
* apply the explanation to the original investigation without expanding the
  roadmap into unrelated implementation work.

The applicable capstone must satisfy its integration criteria. Progress is
shown only by committed, approved Korean session archives with
`status: completed`; there are no progress checkboxes or incomplete session
files.

## Relationship to Other Roadmaps

Security Core is the canonical home for foundational Unix/Linux observation,
general protocol reasoning, and HTTP/browser foundations. Linux Internals is
activated only when those observations reach a deeper OS mechanism boundary.

It is not a fixed prerequisite for Web Security, Bug Bounty Operations, Wired
Network Security, or Wireless Security. Those roadmaps own their security
mechanisms and research workflows; this roadmap supplies only the OS reasoning
needed to unblock a specific investigation.

```text
A security investigation reaches an OS mechanism boundary
↓
Activate the relevant Linux Internals phase
↓
Observe and explain the required mechanism
↓
Return to the original research track
```

Under the normal active-roadmap limit, the investigation remains the primary
roadmap and Linux Internals may be the one optional secondary roadmap.

## Final Outcome

The learner can select and observe the Linux mechanism needed by a security
investigation, reconstruct its runtime or isolation boundary, state the limits
of the evidence, and return a reusable explanation to the original research
track without turning OS study into an unrelated prerequisite.

## Core Mental Model

```text
Security question
↓
Observable user-space state
↓
Kernel interface and mechanism
↓
Identity, isolation, policy, resource, or packet-path boundary
↓
Correlated evidence and alternative explanations
↓
Bounded conclusion returned to the investigation
```
