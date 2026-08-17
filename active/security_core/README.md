# Security Core

## Status

Active.

## Objective

Build the shared observational and protocol-reasoning foundation required by every
specialized security roadmap. The learner must be able to reconstruct what a local
system, network exchange, or web flow did from evidence rather than from assumption.

## Scope

Security Core has three canonical areas:

- Unix/Linux process, descriptor, service, socket, system-call, and log observation;
- Ethernet, IP, routing, transport, name-resolution, and packet reasoning;
- HTTP, TLS, browser state, identity-protocol roles, API forms, and request replay.

This roadmap owns the general foundations in those areas. Specialized roadmaps may
reference them as prerequisites but must not reteach parallel versions of them.

## Non-Goals

This roadmap does not provide:

- vulnerability-class training or public-target testing;
- general systems programming, production administration, or framework mastery;
- deep operating-system internals that are not required for current observation;
- tool-specific curricula for DevTools, Burp Suite, Wireshark, Nmap, or any platform;
- completed-session credit for work from an earlier curriculum version.

## Learning Method

Each session follows this evidence cycle:

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

The learner performs the primary work, records only observed results, separates
evidence from interpretation, and checks plausible alternative explanations. Tools
are interchangeable instruments for answering questions. They are not learning
outcomes or roadmap boundaries.

Sessions are bounded around one central question, no more than five core tasks, and
one explicit completion criterion. Phase and session purposes remain stable, while
fixtures, local services, and exercises may adapt to the learner's environment.

## AI Assistance Boundary

AI use follows [AI-Assisted Learning and
Research](../../ops/docs/AI_ASSISTED_LEARNING_AND_RESEARCH.md). The preferred
maximum assistance varies with demonstrated capability:

| Scope | Preferred maximum | Required human work |
| --- | --- | --- |
| Phase 01 | `explanation` or `review` | Directly inspect processes, descriptors, services, sockets, syscalls, and logs. |
| Phase 02 | `review` | Identify relevant frames, fields, routes, and socket evidence before AI reviews the interpretation. |
| Phase 03 Sessions 01–09 | `review` | Reconstruct the browser and HTTP flow manually before AI review. |
| Phase 03 Session 10 and later | `analysis` | Provide a manual flow model before AI compares evidence or proposes alternatives. |

AI suggestions remain candidates until the learner verifies or rejects them
through targeted direct evidence.

## Lab Environment

Use a local Linux system, virtual machines, containers, or network namespaces that
the learner owns or is explicitly authorized to test. Web exercises may use local
services or authorized training platforms. Capture the smallest useful command,
browser, log, socket, and packet evidence. Never treat an expected result as an
observed fact, and never include sensitive live credentials or target data in the
public repository.

## Prerequisites

No earlier V2 roadmap is required. The learner needs a Linux command-line
environment, permission to observe it, and a willingness to preserve exact evidence.
Any environment-specific setup must remain subordinate to the session question.

## Phase Structure

The phases are sequential:

1. Phase 01 establishes host-side observation.
2. Phase 02 connects host evidence to network protocol behavior.
3. Phase 03 connects network behavior to browser, HTTP, identity, and API state.

Every phase ends in a capstone. A capstone must collect evidence, reconstruct a
state, path, or trust boundary, test alternative explanations, explain the mechanism,
and state limitations and uncertainty.

## Phases and Sessions

### Phase 01 — Unix/Linux Observation

**Goal:** Connect Linux process, descriptor, service, socket, syscall, and log
evidence.

1. **P01-S01 — Local Lab Boundaries and Evidence**
2. **P01-S02 — Processes, PIDs, Parent-Child Relationships and procfs**
3. **P01-S03 — File Descriptors, Streams, Pipes and Redirection**
4. **P01-S04 — Files, Permissions, Ownership and Effective Identity**
5. **P01-S05 — Services, systemd and the Journal**
6. **P01-S06 — Sockets, Endpoints and Owning Processes**
7. **P01-S07 — Correlating strace, lsof, ss, procfs and Logs**
8. **P01-S08 — Capstone — Trace a Local Service End to End**

**Phase completion criterion:** The learner can explain a locally observed flow:

```text
client
→ listening socket
→ process
→ file descriptor
→ system call
→ log entry
→ response
```

### Phase 02 — Network Protocol Reasoning

**Goal:** Explain communication through frames, packets, routes, and transport
state.

1. **P02-S01 — Ethernet Frames and Switching**
2. **P02-S02 — MAC Addresses, ARP and Neighbor State**
3. **P02-S03 — IPv4, Subnetting and CIDR**
4. **P02-S04 — Routing, ICMP and Traceroute**
5. **P02-S05 — TCP State Machine and Reliability**
6. **P02-S06 — UDP, DNS and DHCP**
7. **P02-S07 — NAT, Connection Tracking and Firewalls**
8. **P02-S08 — IPv6 and Neighbor Discovery**
9. **P02-S09 — Packet Capture with tcpdump, TShark and Wireshark**
10. **P02-S10 — Capstone — Explain an End-to-End Packet Flow**

**Phase completion criterion:** The learner can distinguish and correlate:

- local-link decisions;
- next-hop routing;
- source and destination addressing;
- name resolution;
- transport state;
- packet evidence;
- host-side socket evidence.

### Phase 03 — HTTP, Browser and Automation

**Goal:** Reconstruct a complete browser-to-server web flow.

1. **P03-S01 — HTTP Request and Response Lifecycle**
2. **P03-S02 — TLS Handshake, Certificates and Server Identity**
3. **P03-S03 — Cookies, Sessions and Application State**
4. **P03-S04 — Same-Origin Policy, CORS and CSP**
5. **P03-S05 — Browser DevTools, HTTP Proxy and curl**
6. **P03-S06 — JavaScript Control Flow, fetch and XHR**
7. **P03-S07 — REST, JSON and GraphQL Fundamentals**
8. **P03-S08 — Authentication, OAuth, OIDC and Token Roles**
9. **P03-S09 — Python Request Replay, Normalization and Response Diffing**
10. **P03-S10 — AI-Assisted Flow Review and Human Verification**
11. **P03-S11 — Capstone — Reconstruct a Complete Web Flow**

P03-S10 teaches this bounded review workflow:

```text
manual flow reconstruction
↓
AI review
↓
candidate missing links or alternative explanations
↓
targeted browser, proxy, curl, or server verification
↓
accepted or rejected corrections
```

The session applies AI to an existing learner-produced flow model. It does not
teach general prompt engineering.

**Phase completion criterion:** The learner can manually reconstruct and
explain:

```text
browser action
→ JavaScript initiator
→ HTTP request
→ authentication state
→ server response
→ browser-side state change
```

The learner can then use AI to review that model, verify or reject every
material suggestion through targeted evidence, and document the assistance
level and validation outcome.

## Completion Criteria

Security Core is complete when all three phase capstones have committed completed
session archives and the learner can independently:

- correlate host, socket, packet, HTTP, and browser evidence without conflating it;
- reconstruct the required local-service, packet-flow, and browser-flow paths;
- identify which claims are observed, inferred, unresolved, or disproven;
- reject alternative explanations with proportionate evidence;
- explain limitations without hiding uncertainty.

Completion is determined from committed session files with `status: completed`, not
from chat history, checkboxes, tool output alone, or earlier curriculum work.

## Relationship to Other Roadmaps

Security Core is the prerequisite foundation for Web Security and Bug Bounty
Operations. Its general protocol reasoning also supports Wired Network Security,
which in turn supports Wireless Security.

- Security Core owns HTTP and browser fundamentals; Web Security owns web and API
  vulnerability mechanisms.
- Security Core owns general packet and protocol reasoning; Wired Network Security
  owns enterprise LAN, segmentation, and identity security.
- Bug Bounty Operations owns scope, evidence, validation, and reporting workflows.
- Linux Internals is an optional support path when an investigation reaches an
  operating-system mechanism boundary. After resolving that boundary, the learner
  returns to the original research track.

The normal limit is one primary roadmap plus zero or one secondary roadmap. Security
Core is initially the sole primary roadmap.

## Final Outcome

The learner can begin a specialized security track with a defensible habit of
observing systems, correlating multiple evidence sources, reconstructing protocol and
trust-boundary behavior, rejecting false explanations, and recording durable Korean
learning evidence.

## Core Mental Model

System behavior is a connected chain of state transitions visible through different
evidence surfaces. Begin with scope and a precise question, predict what each surface
should show, observe the actual surfaces, correlate them by identity and time, test
competing explanations, and record only what the evidence supports.

```text
Scoped question and prediction
↓
Host, packet, HTTP, and browser evidence
↓
Correlated state transitions
↓
Alternative-explanation checks
↓
Bounded mechanism explanation
```
