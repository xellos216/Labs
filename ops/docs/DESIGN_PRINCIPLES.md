# Design Principles

## Purpose

Labs Curriculum V2 develops security-research judgment through authorized,
observable, and reproducible investigation. It connects host evidence,
protocol state, browser behavior, identity, trust boundaries, validation, and
reporting without treating any one tool as the curriculum.

Success is the ability to explain what happened, identify the evidence that
supports the explanation, reject plausible alternatives, state uncertainty,
and reproduce the reasoning safely.

## Evidence-Based Research Cycle

Use this cycle for learning sessions, capstones, and research operations:

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

Scope comes first because authorization and intended learning boundaries
determine which observations and tests are valid. A result that was not
observed must never be recorded as fact.

## One Concept, One Canonical Home

Foundational concepts have one canonical curriculum location:

| Concept | Canonical home |
| --- | --- |
| Unix/Linux observation and general packet reasoning | Security Core |
| HTTP and browser fundamentals | Security Core |
| Web authentication, authorization, and vulnerability mechanisms | Web Security |
| Scope, evidence, validation, and reporting | Bug Bounty Operations |
| Enterprise LAN, segmentation, and identity security | Wired Network Security |
| IEEE 802.11 and Wi-Fi security | Wireless Security |
| Optional operating-system mechanism depth | Linux Internals |

Another roadmap may reference a prerequisite, use it in a capstone, or deepen
an application-specific consequence. It must not reteach the same foundation
as a parallel canonical curriculum.

## Tools Are Instruments, Not Roadmaps

DevTools, Burp Suite, Wireshark, Nmap, Flask, HTB, PortSwigger Web Security
Academy, HackerOne, and similar tools or environments support an investigation.
They do not define standalone roadmaps. Exercises should remain valid when an
equivalent instrument or authorized fixture is substituted.

## Evidence Before Interpretation

Record observed output, state, packets, logs, or browser behavior separately
from the explanation. Mark inference and uncertainty explicitly. When several
mechanisms could explain a result, design the smallest controlled check that
distinguishes them.

False-positive rejection is part of successful research. An inconclusive or
disconfirmed hypothesis remains useful when its evidence and limitations are
recorded accurately.

## Bounded but Coherent Sessions

A normal session contains:

- one central question
- no more than five core tasks
- one explicit completion criterion
- related tasks from one coherent workstream

Setup, review, and approved archive generation are not extra core tasks. A
correction remains part of the current task. Do not silently turn unrelated
troubleshooting, installation, or repository maintenance into curriculum work.

## Capstone-Based Completion

Every phase ends with a capstone. The capstone must integrate:

- evidence collection
- reconstruction of a state, path, or trust boundary
- checks for alternative explanations
- a mechanism-based explanation
- limitations and uncertainty

Topic coverage alone is not phase completion. The learner must demonstrate the
phase outcome using observed evidence.

## Stable Boundaries, Adaptive Exercises

Roadmap phase and session purposes remain stable. Fixtures, local services,
accounts, tools, commands, and exercises may adapt to the learner's actual
environment when the adaptation preserves the question, evidence requirement,
authorization boundary, and completion criterion.

Do not change roadmap sequence or add canonical topics silently. Record and
review curriculum changes as repository changes.

## Learner Performs the Primary Work

The learner should perform the primary terminal, browser, capture, and
analysis work. The assistant supplies bounded setup, questions, review, and
minimal corrections before explaining the mechanism. Prediction before
observation is preferred when it improves learning.

## Durable Korean Learning Records

Governance and roadmap specifications are English. A completed session archive
is one Korean Markdown file that preserves the learner's prediction, observed
evidence, explanation, failures, uncertainty, and reusable mental model.
Commands and technical identifiers retain their original form.

The archive is created only after actual completion and learner approval. It
is a record of observed work, not a prospective worksheet or chat transcript.

## Authorization and Publication Safety

Practical investigation must stay inside local, owned, isolated, or explicitly
authorized environments. Authorization, program scope, regulatory limits, and
data-handling rules are part of the experiment design.

The public repository contains only publication-safe material. Private program
data, undisclosed findings, credentials, live tokens, personal account data,
unredacted captures, confidential source, and unsafe target identifiers belong
in a separate private workspace.

## Decision Priorities

When approaches compete, prefer in this order:

1. Authorization and safety
2. Factual and technical correctness
3. Observable evidence
4. False-positive control
5. Reproducibility
6. Transferable mental models
7. Simplicity and maintainability

Automation is valuable only when its inputs, transformations, outputs, noise,
and limitations remain inspectable.
