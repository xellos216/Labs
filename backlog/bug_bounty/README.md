# Bug Bounty Operations

## Status

Backlog

## Objective

Develop a repeatable method for selecting, validating, documenting, and scaling
authorized security research. The learner must be able to convert program policy
into test boundaries, maintain evidence-backed hypotheses, reject false
positives, calibrate impact, and produce reproducible reports.

## Scope

- Program scope, authorization boundaries, asset ownership, and target mapping.
- Passive reconnaissance and controlled HTTP, JavaScript, API, account, role,
  and application-state inventories.
- Hypothesis ledgers, test matrices, minimal reproduction, evidence provenance,
  and alternative-explanation checks.
- Impact calibration, classification, reporting, triage communication, and
  retesting.
- Transparent request processing, response comparison, cross-account replay,
  and other bounded research automation.

## Non-Goals

- Teaching web vulnerability mechanisms; their canonical home is Web Security.
- Testing outside explicit authorization or disregarding program policy.
- Indiscriminate enumeration, noisy automation, or ranking-oriented activity.
- Treating HackerOne, any other platform, or a research tool as a roadmap.
- Using bounty amount, platform rank, or report volume as completion evidence.
- Publishing private-program data, undisclosed vulnerabilities,
  scope-restricted targets, live authentication material, personal account
  data, confidential source, prohibited exploit material, or unsafe real-target
  identifiers.

## Learning Method

Each investigation follows this cycle:

Scope → Question → Prediction or Hypothesis → Observation or Controlled Test →
Evidence → Explanation → Record

A normal session has one central question, no more than five core tasks, one
explicit completion criterion, and one coherent workstream. The learner performs
the primary work. Tools are instruments for collecting and comparing evidence,
not substitutes for reasoning. Only observed results may be recorded as facts.
Every phase ends with a capstone that integrates evidence collection,
reconstruction of a state or trust boundary, alternative-explanation checks,
explanation, limitations, and uncertainty.

## Lab Environment

Work may use local applications, owned test systems, isolated labs, explicitly
authorized training platforms, public disclosed-report material, and targets
covered by current written program scope. Authorization must be established
before any active test and rechecked when scope or ownership is uncertain.

Private-program evidence belongs in a separate private workspace. This public
repository may contain local lab results, authorized training-platform results,
public disclosed-report analysis, sanitized mechanism notes, redacted examples,
and generic reusable workflows. It must not contain private program data,
undisclosed vulnerability details, scope-restricted target information, live
cookies or tokens, credentials or secrets, personal account data, unredacted
packet captures, confidential third-party source, prohibited exploit material,
or real target identifiers when disclosure is unsafe.

## Prerequisites

- Security Core completed, or equivalent evidence for its Linux, network, HTTP,
  browser, identity, and automation outcomes.
- The relevant Web Security mechanism completed before using it in target
  research; this roadmap does not replace that technical foundation.
- An environment where account roles, requests, responses, and evidence can be
  handled within explicit authorization and publication rules.

## Phase Structure

| Phase | Purpose | Capstone |
| --- | --- | --- |
| Phase 01 | Establish authorization and an evidence-backed attack surface | Build an Evidence-Backed Target Map |
| Phase 02 | Validate observations and communicate defensible results | Confirmed, False-Positive and Inconclusive Reports |
| Phase 03 | Scale repeatable reasoning without hiding evidence | Build a Transparent Research Harness |

## Phases and Sessions

### Phase 01 — Scope and Attack-Surface Mapping

**Goal:** Convert current program policy and observed surfaces into an
evidence-backed, testable target map.

1. **P01-S01 — Program Policy as an Authorization Boundary**
2. **P01-S02 — Asset Inventory and Ownership Confidence**
3. **P01-S03 — Passive Reconnaissance**
4. **P01-S04 — HTTP Surface Mapping**
5. **P01-S05 — JavaScript and API Surface Mapping**
6. **P01-S06 — Account, Role and State Inventory**
7. **P01-S07 — Hypothesis Ledger and Test Matrix**
8. **P01-S08 — Capstone — Build an Evidence-Backed Target Map**

**Phase completion criterion:** The learner can produce an evidence-backed
target map that connects current authorization, ownership confidence, observed
HTTP, JavaScript, and API surfaces, account and state inventories, and testable
hypotheses while marking unresolved scope or attribution uncertainty.

### Phase 02 — Validation and Reporting

**Goal:** Validate observations, reject unsupported explanations, and communicate
defensible results that another authorized reviewer can reproduce.

1. **P02-S01 — Minimal Reproduction**
2. **P02-S02 — Evidence Bundles and Provenance**
3. **P02-S03 — False Positives and Alternative Explanations**
4. **P02-S04 — Impact Calibration**
5. **P02-S05 — Duplicate, Out-of-Scope and Non-Security Classification**
6. **P02-S06 — Writing a Reproducible Report**
7. **P02-S07 — Triage Communication and Retesting**
8. **P02-S08 — Capstone — Confirmed, False-Positive and Inconclusive Reports**

**Phase completion criterion:** The learner can present confirmed,
false-positive, and inconclusive cases with minimal reproductions, evidence
provenance, alternative-explanation checks, calibrated impact, defensible
classification, reproducible reports, and retest results.

### Phase 03 — Research Scaling

**Goal:** Scale validated manual reasoning through bounded automation whose
inputs, decisions, and evidence remain inspectable.

1. **P03-S01 — Manual Reasoning Before Automation**
2. **P03-S02 — Request Corpus and HAR Processing**
3. **P03-S03 — Response Normalization and Diffing**
4. **P03-S04 — Cross-Account and Cross-Role Replay**
5. **P03-S05 — Concurrency, Rate Limits and Noise Control**
6. **P03-S06 — Structured Evidence Storage**
7. **P03-S07 — Specialization and Research Retrospectives**
8. **P03-S08 — Capstone — Build a Transparent Research Harness**

**Phase completion criterion:** The learner can demonstrate a bounded harness
whose request corpus, normalization, comparisons, account and role changes,
concurrency controls, and stored evidence are inspectable, with a representative
result reproduced manually and remaining uncertainty recorded.

## Completion Criteria

The roadmap is complete when committed completed-session archives show that the
learner can:

- derive a test boundary from current program policy and produce an
  evidence-backed target map;
- maintain hypotheses and test matrices across assets, accounts, roles, and
  states;
- produce minimal reproductions with provenance and distinguish confirmed,
  false-positive, and inconclusive results;
- calibrate impact and write a report that another authorized reviewer can
  reproduce;
- build a bounded harness whose inputs, transformations, comparisons, rate
  controls, and outputs remain inspectable; and
- complete every capstone with collected evidence, reconstructed state or trust
  boundaries, rejected alternatives, an explanation, and explicit limitations
  and uncertainty.

Valid progress indicators are:

- reproducible findings;
- false-positive rejection quality;
- evidence completeness;
- multi-account and multi-role reasoning;
- impact calibration;
- report clarity;
- depth within a target or mechanism;
- reusable hypotheses; and
- transparent automation.

HackerOne ranking, any other platform ranking, bounty amount, and report volume
are not curriculum completion criteria.

## Relationship to Other Roadmaps

Security Core supplies the shared observation, protocol, HTTP, browser,
identity, and automation foundation. Web Security explains how web and API
vulnerability mechanisms work; Bug Bounty Operations explains how authorized
research is selected, validated, documented, and repeated. This roadmap applies
those mechanisms without reteaching them.

Wired Network Security and Wireless Security own their respective network
assessment domains. Linux Internals is an optional support path when an
investigation reaches an operating-system mechanism boundary. Under the normal
activation limit, Bug Bounty Operations may be the primary roadmap or a single
secondary roadmap alongside one primary technical track.

## Final Outcome

The learner can run an authorized research workstream from policy review through
target mapping, hypothesis management, controlled validation, false-positive
rejection, impact analysis, reproducible reporting, and transparent scaling
without losing evidence provenance or publication safety.

## Core Mental Model

Authorization defines what may be tested. A hypothesis defines what evidence is
needed. A controlled test changes or compares one relevant condition. Evidence
supports or rejects the hypothesis. A report preserves the boundary, method,
result, alternatives, impact, limitations, and provenance so another authorized
reviewer can evaluate the same claim.

```text
Authorization → Hypothesis → Controlled test → Evidence
                                                ↓
Alternative explanations ───────────────→ Validate or reject
                                                ↓
                                    Reproducible report
```
