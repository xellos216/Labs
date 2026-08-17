# Web Security

## Status

Backlog.

This roadmap is a complete curriculum specification but is not active in the initial
V2 state.

## Objective

Develop the ability to identify, reconstruct, validate, and explain web and API
vulnerability mechanisms across identity, browser, parser, intermediary, workflow,
and source-code trust boundaries.

## Scope

Web Security is the primary technical track for web and API vulnerability research.
It owns:

- authentication, session, and authorization failure mechanisms;
- browser and client-side trust boundaries;
- server-side parser and input-interpretation vulnerabilities;
- API and GraphQL authorization behavior;
- HTTP intermediary and protocol interpretation mismatches;
- business-logic and concurrency failures;
- source-assisted reconstruction of security-relevant request and data flow.

Foundational HTTP, browser, identity-protocol, and packet concepts remain canonical
in Security Core. Research selection, program scope, evidence operations, and report
workflow remain canonical in Bug Bounty Operations.

## Non-Goals

This roadmap does not provide:

- frontend application development;
- framework mastery for its own sake;
- memorized payload collections;
- indiscriminate public-target testing;
- ranking-oriented activity without valid research output.

It also does not create tool- or platform-specific curricula. Burp Suite, browser
DevTools, curl, local frameworks, PortSwigger Web Security Academy, HTB, and similar
resources may be used as instruments or authorized practice environments.

## Learning Method

Each session begins with an authorized scope and one central technical question. The
learner states a prediction or hypothesis, performs a controlled test, preserves the
minimum evidence needed, separates observation from interpretation, checks plausible
alternative explanations, and records the conclusion.

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

A normal session contains no more than five core tasks and one explicit completion
criterion. Phase and session purposes are stable, while fixtures, application stacks,
and exercises may adapt to the authorized environment. Tools are instruments, not
roadmaps or substitutes for mechanism reasoning.

## Lab Environment

Use deliberately vulnerable local applications, owned containers or virtual
machines, reviewable source fixtures, or explicitly authorized training platforms.
Multi-account and multi-role exercises require accounts controlled by the learner or
provided for the lab. Do not test public systems without explicit authorization, and
do not commit private-program data, live secrets, unsafe target identifiers, or
undisclosed vulnerability evidence to this public repository.

## Prerequisites

Security Core completed or equivalent evidence. The learner must already be able to
reconstruct HTTP requests and responses, browser state, authentication state, and
the relevant packet or host evidence without treating tool output as self-explanatory.

## Phase Structure

The seven phases move from identity and browser boundaries through server-side
interpretation, APIs, intermediaries, application workflows, and source-assisted
review. Every phase ends in a capstone that must:

- collect evidence with clear provenance;
- reconstruct a state, path, parser boundary, or trust boundary;
- test plausible alternative explanations and false-positive conditions;
- explain the demonstrated mechanism and impact;
- state limitations and unresolved uncertainty.

## Phases and Sessions

### Phase 01 — Identity, Session and Access Control

**Goal:** Reconstruct identity and session state, then validate server-side
authorization across controlled accounts and roles.

1. **P01-S01 — Authentication as a State Machine**
2. **P01-S02 — Session Creation, Rotation and Expiration**
3. **P01-S03 — Multi-Account and Multi-Role Test Matrices**
4. **P01-S04 — Server-Side Authorization and IDOR**
5. **P01-S05 — Object, Function and Property Authorization**
6. **P01-S06 — Password Reset, Account Recovery and MFA State**
7. **P01-S07 — OAuth and OIDC Trust Boundaries**
8. **P01-S08 — Capstone — Identity and Access Review**

**Phase completion criterion:** The learner can reconstruct an identity and session
state machine, execute a controlled multi-account and multi-role authorization
matrix, and distinguish a server-side access-control failure from client behavior or
test contamination.

### Phase 02 — Browser and Client-Side Security

**Goal:** Trace input and browser state across client-side trust and policy
boundaries.

1. **P02-S01 — Input Contexts, Sources and Sinks**
2. **P02-S02 — Reflected and Stored XSS**
3. **P02-S03 — DOM-Based XSS**
4. **P02-S04 — CSRF and SameSite Behavior**
5. **P02-S05 — CORS Misconfiguration**
6. **P02-S06 — CSP, postMessage and Clickjacking Boundaries**
7. **P02-S07 — JavaScript Bundles and Source Maps**
8. **P02-S08 — Capstone — Browser Trust-Boundary Review**

**Phase completion criterion:** The learner can trace attacker-controlled input and
browser state across sources, sinks, origins, windows, and policy boundaries, then
validate an observed effect without confusing browser protection with server-side
authorization.

### Phase 03 — Server-Side Input Interpretation

**Goal:** Explain how user input crosses server-side parser boundaries and
validate resulting behavior without accepting noise as evidence.

1. **P03-S01 — User Input Crossing Parser Boundaries**
2. **P03-S02 — SQL Injection**
3. **P03-S03 — Command Injection**
4. **P03-S04 — Server-Side Template Injection**
5. **P03-S05 — Path Traversal and File Inclusion**
6. **P03-S06 — File Upload Processing**
7. **P03-S07 — SSRF and URL Parsing**
8. **P03-S08 — XML, Deserialization and Object Reconstruction**
9. **P03-S09 — Blind Observation, Timing and False-Positive Control**
10. **P03-S10 — Capstone — Multi-Parser Vulnerability Analysis**

**Phase completion criterion:** The learner can identify each parser and trust
transition crossed by input, choose evidence appropriate to direct or blind behavior,
and reject timing noise or other alternative explanations before reporting a result.

### Phase 04 — API and GraphQL Security

**Goal:** Map API state and validate object, function, and property
authorization across identities, sequences, and versions.

1. **P04-S01 — Endpoint and Schema Discovery**
2. **P04-S02 — Object-Level Authorization**
3. **P04-S03 — Function-Level Authorization**
4. **P04-S04 — Property-Level Authorization and Mass Assignment**
5. **P04-S05 — Sequence, Replay and Rate-Dependent Behavior**
6. **P04-S06 — GraphQL Queries, Mutations and Authorization**
7. **P04-S07 — Hidden, Deprecated and Versioned Endpoints**
8. **P04-S08 — Capstone — API Authorization Review**

**Phase completion criterion:** The learner can build an evidence-backed endpoint and
schema map, distinguish object, function, and property authorization, and validate
results across controlled identities, sequences, and versions.

### Phase 05 — HTTP Intermediaries and Protocol Edge Cases

**Goal:** Reconstruct how intermediaries transform and interpret requests,
then validate meaningful frontend and backend mismatches.

1. **P05-S01 — Proxies, Reverse Proxies and Backend Boundaries**
2. **P05-S02 — Host Header Trust**
3. **P05-S03 — Cache Keys, Cache Poisoning and Cache Deception**
4. **P05-S04 — Request Smuggling and HTTP Desynchronization**
5. **P05-S05 — Duplicate Headers, Parameters and Ambiguous Parsing**
6. **P05-S06 — HTTP/2 Translation and Downgrade Boundaries**
7. **P05-S07 — CDN and Load-Balancer Inconsistencies**
8. **P05-S08 — Capstone — Frontend and Backend Interpretation Mismatch**

**Phase completion criterion:** The learner can reconstruct how each intermediary
interprets and transforms a request, demonstrate a meaningful mismatch with bounded
evidence, and eliminate cache, connection-reuse, and environmental noise as competing
explanations.

### Phase 06 — Business Logic and Race Conditions

**Goal:** Model application state and invariants, then test sequence,
concurrency, replay, and cross-feature failures under controlled conditions.

1. **P06-S01 — Application State Machines**
2. **P06-S02 — Workflow and Sequence Bypass**
3. **P06-S03 — Pricing, Quantity and Invariant Violations**
4. **P06-S04 — Race Conditions and Concurrency**
5. **P06-S05 — Idempotency and Replay**
6. **P06-S06 — Cross-Feature Vulnerability Chaining**
7. **P06-S07 — Impact Chains and Realistic Abuse Cases**
8. **P06-S08 — Capstone — Business-Logic Investigation**

**Phase completion criterion:** The learner can model an application state machine
and its invariants, reproduce a sequence or concurrency failure under controlled
conditions, and explain a realistic impact chain without overstating reachability or
reliability.

### Phase 07 — Source-Assisted Security Review

**Goal:** Correlate source paths with runtime evidence to trace security
decisions from entry point to sensitive operation.

1. **P07-S01 — Architecture and Entry-Point Mapping**
2. **P07-S02 — Routes, Middleware and Request Flow**
3. **P07-S03 — Authentication and Authorization Call Paths**
4. **P07-S04 — Data Flow from Input to Sensitive Sink**
5. **P07-S05 — Patch, Advisory and Regression Analysis**
6. **P07-S06 — Capstone — Source-Assisted Vulnerability Report**

**Phase completion criterion:** The learner can correlate source paths with runtime
evidence, trace input and identity decisions to a sensitive operation, compare a
patch or advisory with observed behavior, and produce a reproducible report that
states uncertainty.

## Completion Criteria

Web Security is complete when every phase capstone has a committed completed-session
archive and the learner can independently:

- select evidence appropriate to the mechanism under test;
- reconstruct relevant identity, browser, parser, API, intermediary, workflow, and
  source-code boundaries;
- validate behavior across controlled state, account, role, sequence, and timing
  changes;
- reject false positives and document unresolved alternatives;
- explain demonstrated impact without exceeding the evidence or authorization scope.

Progress is determined from committed files with `status: completed`, not checkboxes,
chat history, payload count, platform rank, or bounty amount.

## Relationship to Other Roadmaps

- Security Core is the required foundation and remains the canonical home for HTTP,
  browser, protocol, and identity-role fundamentals.
- Web Security owns vulnerability mechanisms; Bug Bounty Operations owns how
  authorized research is selected, validated, documented, reported, and repeated.
- Bug Bounty Operations may run as the optional secondary roadmap when its workstream
  remains coherent with active Web Security research.
- Wired Network Security follows the primary web-security foundation in the suggested
  progression and applies protocol reasoning to enterprise LAN and identity systems.
- Linux Internals may be activated temporarily when an investigation reaches an
  operating-system mechanism boundary; it is not a fixed prerequisite.

The normal active limit remains one primary roadmap plus zero or one secondary
roadmap.

## Final Outcome

The learner can conduct an authorized web or API security review that connects a
precise hypothesis to reproducible evidence, reconstructs the responsible trust
boundary or state transition, rejects plausible false positives, and explains impact
and limitations in a technically defensible report.

## Core Mental Model

A web vulnerability is a demonstrated mismatch between intended and enforced trust,
state, or interpretation boundaries. Map the actors and state, identify where data or
authority crosses a boundary, predict the valid and invalid behavior, vary one
controlled condition at a time, and accept a finding only when evidence survives
alternative-explanation checks.

```text
Actors and state
↓
Trust or interpretation boundary
↓
Controlled comparison
↓
Observed security-relevant mismatch
↓
Alternative checks and bounded conclusion
```
