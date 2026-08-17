# Wireless Security

## Status

Backlog

## Objective

Build the ability to observe IEEE 802.11 behavior, explain Wi-Fi security
state, and conduct reproducible assessments inside authorized boundaries. The
roadmap emphasizes packet evidence, state-machine reasoning, alternative
explanations, and reporting rather than tool output alone.

## Scope

The initial roadmap covers Wi-Fi and IEEE 802.11 only:

* RF conditions that affect observation
* Linux wireless interfaces and adapter capabilities
* 802.11 frames, roles, and connection state
* WPA2, WPA3, protected management, and enterprise authentication
* guest isolation, segmentation, and management boundaries
* authorized wireless assessment and evidence handling

## Non-Goals

The initial roadmap does not cover:

* BLE
* NFC
* cellular networks
* general SDR
* firmware analysis
* IoT device exploitation

It also excludes testing outside an owned, isolated, or explicitly authorized
environment. Adapter commands, capture suites, and assessment utilities are
instruments within sessions, not separate roadmaps or completion goals.

## Learning Method

Each session follows the same evidence cycle:

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

The learner performs the primary capture and inspection work. Results are
accepted only when supported by observed evidence. Failed hypotheses and
alternative explanations remain part of the record when they clarify the
mechanism.

## Lab Environment

Practical work must use an isolated AP/client lab, owned equipment, or an
explicitly authorized platform. The environment should provide:

* a Linux observation host;
* a compatible wireless adapter with documented mode capabilities;
* an AP and client whose configuration the learner may change;
* packet capture and host-side logging;
* controlled channels and a reversible network configuration.

Capture capability does not imply injection capability, and neither implies
authorization. Regulatory restrictions and the physical reach of RF signals
remain part of the test boundary.

Public records may contain sanitized local-lab evidence, authorized
training-platform results, and reusable workflows. They must not contain live
credentials, cookies, private-program data, undisclosed vulnerabilities,
unsafe real-target identifiers, or unredacted packet captures. Private
evidence belongs in a separate private workspace.

## Prerequisites

### Activation Gate

Activate this roadmap only when all of the following are true:

* Security Core Network Protocol Reasoning completed;
* compatible wireless adapter verified;
* isolated AP/client lab available;
* regulatory and authorization boundaries understood;
* capture and injection capabilities explicitly distinguished.

## Phase Structure

The roadmap contains three stable phases. Sessions within a phase move from
observation to protocol explanation and then to a bounded assessment. Each
phase ends with a capstone that requires evidence collection, reconstruction
of state or a trust boundary, alternative-explanation checks, an explanation,
and explicit limitations and uncertainty.

Exercises and fixtures may adapt to the available adapter and lab, but phase
purposes, session purposes, and completion criteria remain stable. A session
normally has one central question, no more than five core tasks, and one
explicit completion criterion.

## Phases and Sessions

### Phase 01 — RF and 802.11 Observation

Goal: reconstruct how an authorized client discovers, joins, and exchanges
data with an AP by correlating RF conditions, interface state, and 802.11
frames.

1. **P01-S01 — RF, Channels, Bandwidth, Noise and SNR**
2. **P01-S02 — Linux Wireless Stack and Adapter Capabilities**
3. **P01-S03 — Managed Mode, Monitor Mode and Radiotap**
4. **P01-S04 — Management, Control and Data Frames**
5. **P01-S05 — Beacon and Probe Behavior**
6. **P01-S06 — Authentication and Association State Machines**
7. **P01-S07 — Data Frames, Clients, BSS and Distribution**
8. **P01-S08 — Channel Use and Roaming Behavior**
9. **P01-S09 — Wireless Capture and Packet-Narrative Workflow**
10. **P01-S10 — Capstone — Explain a Complete 802.11 Connection**

**Phase completion criterion:** The learner can reconstruct one authorized
802.11 connection by correlating RF and channel conditions, interface
capabilities, management and data frames, state transitions, and host evidence,
while stating alternative explanations and evidence limits.

### Phase 02 — Wi-Fi Security Protocols

Goal: compare personal and enterprise Wi-Fi security by explaining the
identities, key-establishment state, replay protections, certificate checks,
and segmentation assumptions visible in controlled evidence.

1. **P02-S01 — WPA2-Personal**
2. **P02-S02 — EAPOL and the Four-Way Handshake**
3. **P02-S03 — Key Derivation, Replay Counters and Session State**
4. **P02-S04 — WPA3 and SAE**
5. **P02-S05 — Protected Management Frames**
6. **P02-S06 — WPS and Configuration Risk**
7. **P02-S07 — 802.1X and EAP**
8. **P02-S08 — RADIUS and Certificate Validation**
9. **P02-S09 — Guest Networks, Client Isolation and Segmentation**
10. **P02-S10 — Capstone — Compare Personal and Enterprise Wi-Fi Security**

**Phase completion criterion:** The learner can compare personal and
enterprise Wi-Fi by reconstructing observed authentication, key establishment,
replay, certificate, management-frame, and segmentation boundaries without
treating an expected protocol step as observed fact.

### Phase 03 — Authorized Wireless Assessment

Goal: validate wireless configuration and trust boundaries in an authorized
lab, reject unsupported findings, and produce a reproducible report.

1. **P03-S01 — Scope, Site Survey and Evidence Boundaries**
2. **P03-S02 — AP, Client and Security-Mode Inventory**
3. **P03-S03 — Wireless Misconfiguration Test Matrix**
4. **P03-S04 — Rogue AP and Evil-Twin Conditions in an Isolated Lab**
5. **P03-S05 — Enterprise Authentication and Certificate Failure Modes**
6. **P03-S06 — AP Management and Control Plane**
7. **P03-S07 — Wired and Wireless Segmentation**
8. **P03-S08 — Capstone — Wireless Assessment and Report**

**Phase completion criterion:** The learner can produce a sanitized,
reproducible assessment that maps AP, client, authentication, management, and
segmentation boundaries; validates or rejects each finding with controlled
evidence; and reports impact, limitations, and uncertainty.

## Completion Criteria

The roadmap is complete when the learner can:

* reconstruct a complete 802.11 connection from packet and host evidence;
* distinguish RF conditions from protocol state and security controls;
* compare personal and enterprise authentication and key-establishment paths;
* validate AP, client, identity, and segmentation boundaries without exceeding
  authorization;
* reject false positives through controlled comparison and alternative
  explanations;
* report evidence, impact, limitations, and uncertainty reproducibly.

Every phase capstone must satisfy its integration criteria. Progress is shown
only by committed, approved Korean session archives with `status: completed`;
there are no progress checkboxes or incomplete session files.

## Relationship to Other Roadmaps

Security Core is the canonical home for general packet, routing, transport,
HTTP, and host-side socket reasoning. Wireless Security applies that foundation
to 802.11 and does not reteach it as a parallel curriculum.

Wired Network Security owns enterprise LAN, identity, and multi-segment
assessment. This roadmap owns the Wi-Fi link, wireless authentication, RF
observation, and the boundary between wireless access and wired segmentation.
Web Security owns web and API vulnerability mechanisms, while Bug Bounty
Operations owns scope, validation, evidence, and reporting workflows for
authorized public research.

The suggested primary progression is Security Core, then Web Security and Bug
Bounty Operations, then Wired Network Security, and then Wireless Security.
Linux Internals remains an optional support path when an investigation reaches
an operating-system mechanism boundary.

## Final Outcome

The learner can explain a Wi-Fi connection and its security controls from
observed evidence, design a bounded test matrix, distinguish findings from
environmental noise, and deliver a sanitized assessment report without
exceeding regulatory or authorization limits.

## Core Mental Model

```text
Authorization and regulatory boundary
↓
RF environment and channel
↓
802.11 role, frame, and state transition
↓
Authentication and key-establishment boundary
↓
Protected data path
↓
Wired segmentation and service reachability
↓
Evidence, alternative explanations, and report
```
