# Wired Network Security

## Status

Backlog

## Objective

Analyze wired LAN behavior, segmentation, enterprise protocols, and identity
paths through packet, host, directory, policy, and log evidence. The learner must
be able to map trust boundaries, reconstruct authentication and authorization,
and assess reachability across multiple authorized segments.

## Scope

- Ethernet switching, VLANs, broadcast domains, local-network control
  protocols, IPv6, and segmentation.
- 802.1X, RADIUS, NAC, enterprise directory services, authentication protocols,
  authorization data, and certificate services.
- Discovery, enumeration, administrative surfaces, tunneling, routing, relay and
  delegation concepts, reachability, and defensive visibility.
- Evidence-based assessment of LAN and enterprise identity paths in controlled
  multi-segment environments.

## Non-Goals

- Reteaching general packet and protocol reasoning; its canonical home is
  Security Core.
- Wi-Fi or IEEE 802.11 security; its canonical home is Wireless Security.
- General production administration, broad infrastructure engineering, or
  tool-specific curricula.
- Unscoped discovery, credential use, traffic interception, movement, or
  exploitation on third-party or production networks.
- Publishing credentials, secrets, personal data, private assessment details,
  unredacted packet captures, confidential source, or unsafe target identifiers.

## Learning Method

Each investigation follows this cycle:

Scope → Question → Prediction or Hypothesis → Observation or Controlled Test →
Evidence → Explanation → Record

A normal session has one central question, no more than five core tasks, one
explicit completion criterion, and tasks from one coherent workstream. The
learner performs the primary work and correlates packet evidence with host,
route, identity, policy, and log state. Tools are instruments rather than
roadmaps. Every phase ends with a capstone that collects evidence, reconstructs
a path or trust boundary, checks alternative explanations, explains the observed
mechanism, and records limitations and uncertainty.

## AI Assistance Boundary

Under [AI-Assisted Learning and
Research](../../ops/docs/AI_ASSISTED_LEARNING_AND_RESEARCH.md), AI may compare
packet traces, organize protocol timelines, review network diagrams, generate
candidate trust relationships, compare configurations, suggest verification
questions, and summarize documented protocol behavior.

The learner performs authorization and scope decisions, packet capture, frame
and packet selection, initial field interpretation, command execution, path
and identity verification, and final assessment conclusions. AI cannot infer a
reachable path, credential validity, privilege relationship, or segmentation
bypass without direct evidence. Capstones may use AI review, but the primary
network and identity reconstruction must be human-produced.

## Lab Environment

Every practical activity must remain inside one or more of these environments:

- local virtual machines;
- network namespaces;
- owned networks;
- isolated labs; or
- explicitly authorized platforms.

Use synthetic identities and non-production credentials where practical. Keep
private or scope-restricted evidence in a separate private workspace. The public
repository may contain local lab results, authorized training-platform results,
public disclosed-report analysis, sanitized mechanism notes, redacted examples,
and generic workflows, but not private target data, live credentials or tokens,
personal account data, unredacted captures, confidential third-party source,
prohibited exploit material, or unsafe real-target identifiers.

## Prerequisites

- Security Core completed, or equivalent evidence, including Network Protocol
  Reasoning.
- Web Security completed in the primary V2 progression so application and
  identity trust boundaries can be referenced without duplication.
- An isolated or explicitly authorized environment with observable packet,
  route, service, identity, policy, and log state.

## Phase Structure

| Phase | Purpose | Capstone |
| --- | --- | --- |
| Phase 01 | Map LAN behavior, controls, and segmentation | Map a Wired Network and Its Trust Boundaries |
| Phase 02 | Reconstruct enterprise protocols and identity decisions | Reconstruct an Enterprise Identity Path |
| Phase 03 | Assess authorized reachability across segments | Authorized Multi-Segment Assessment |

## Phases and Sessions

### Phase 01 — LAN and Network-Control Foundations

**Goal:** Map LAN behavior, controls, segmentation, and trust boundaries through
correlated packet and host evidence.

1. **P01-S01 — Building an Isolated Wired Lab**
2. **P01-S02 — Ethernet Switching, VLANs and Broadcast Domains**
3. **P01-S03 — ARP, DHCP and DNS Trust Assumptions**
4. **P01-S04 — IPv6 and Neighbor Discovery on Local Networks**
5. **P01-S05 — 802.1X, RADIUS and NAC Concepts**
6. **P01-S06 — Discovery and Enumeration with Packet Evidence**
7. **P01-S07 — Segmentation and Firewall Reasoning**
8. **P01-S08 — Capstone — Map a Wired Network and Its Trust Boundaries**

**Phase completion criterion:** The learner can correlate packet and host
evidence to map links, VLANs, broadcast domains, address and neighbor state,
network-control services, access-control assumptions, and observed segmentation,
including alternative explanations and unresolved visibility gaps.

### Phase 02 — Enterprise Protocols and Identity

**Goal:** Reconstruct an enterprise identity decision across directory,
authentication, authorization, service, certificate, and log boundaries.

1. **P02-S01 — Windows and Active Directory Topology**
2. **P02-S02 — SMB and RPC**
3. **P02-S03 — LDAP and Directory Objects**
4. **P02-S04 — Kerberos Authentication**
5. **P02-S05 — NTLM Authentication**
6. **P02-S06 — Active Directory DNS**
7. **P02-S07 — WinRM, RDP and Remote Administration Surfaces**
8. **P02-S08 — Users, Groups, ACLs and Group Policy**
9. **P02-S09 — Enterprise PKI and Certificate Services**
10. **P02-S10 — Capstone — Reconstruct an Enterprise Identity Path**

**Phase completion criterion:** The learner can reconstruct an observed
enterprise identity path across DNS, directory objects, authentication,
authorization, certificates, and a service boundary, correlate protocol and log
evidence, reject plausible alternatives, and state limitations.

### Phase 03 — Reachability, Movement and Segmentation

**Goal:** Explain authorized multi-segment reachability and its identity,
forwarding, segmentation, and defensive-observation constraints.

1. **P03-S01 — Credentials, Secrets and Identity Material**
2. **P03-S02 — Local and Remote Administrative Surfaces**
3. **P03-S03 — Tunneling and Port Forwarding**
4. **P03-S04 — Multi-Homed Hosts and Route-Based Pivoting**
5. **P03-S05 — Relay and Delegation Concepts**
6. **P03-S06 — Segmentation and Reachability Paths**
7. **P03-S07 — Logs and Defensive Visibility**
8. **P03-S08 — Capstone — Authorized Multi-Segment Assessment**

**Phase completion criterion:** Inside an approved environment, the learner can
reconstruct a multi-segment reachability path across identity material,
administrative surfaces, forwarding boundaries, and segmentation controls,
correlate it with defensive logs, test alternative explanations, and report
uncertainty and limitations.

## Completion Criteria

The roadmap is complete when committed completed-session archives show that the
learner can:

- map a wired network's links, VLANs, broadcast domains, routes, control
  protocols, and segmentation decisions with packet and host evidence;
- reconstruct an enterprise identity path across name resolution, directory
  data, authentication, authorization, certificate, and service boundaries;
- distinguish credentials, identity material, reachability, privilege,
  delegation, and administrative surfaces;
- explain an authorized multi-segment path and correlate it with segmentation
  policy and defensive logs; and
- complete every capstone with collected evidence, reconstructed state or trust
  boundaries, alternative-explanation checks, a mechanism explanation, and
  explicit limitations and uncertainty.

Completion never depends on activity outside the environments permitted by this
roadmap.

## Relationship to Other Roadmaps

Security Core is the canonical home for general packet, route, transport, socket,
and capture reasoning. In the primary progression, Wired Network Security follows
Web Security and applies established trust-boundary reasoning to LAN and
enterprise identity systems without duplicating web vulnerability mechanisms.
Wireless Security follows this roadmap and owns 802.11 and Wi-Fi behavior; this
roadmap owns enterprise LAN, segmentation, and identity security.

Bug Bounty Operations owns scope handling, evidence operations, validation, and
reporting methods that may support an authorized network assessment. Linux
Internals is optional support when host isolation, kernel, or packet-path details
block an active investigation. The normal activation limit is one primary
roadmap plus zero or one secondary roadmap.

## Final Outcome

The learner can conduct an authorized, evidence-based wired network assessment,
explain LAN and enterprise identity behavior end to end, identify meaningful
trust and segmentation boundaries, distinguish observations from conclusions,
and report verified paths with limitations.

## Core Mental Model

A wired assessment traces a path through attachment, link-layer forwarding,
address and route selection, service reachability, identity proof,
authorization, and defensive observation. Each transition is a trust boundary.
Packet, host, directory, policy, and log evidence must agree before a path or
security conclusion is accepted.

```text
Attachment → L2 forwarding → Address and route → Service
     ↓              ↓                ↓              ↓
  Evidence       Evidence         Evidence       Evidence
                                                   ↓
Identity proof → Authorization → Defensive observation
     ↓                 ↓                  ↓
  Evidence           Evidence          Evidence
```
