# Phase 02 - Session 12

## Metadata

```yaml
Roadmap: Networking
Phase: 02
Session: 12
Title: Connection Tracking
Status: Completed
Review: Pending
ArchiveVersion: 3
Date: 2026-07-24
```

---

# Objective

Understand how Linux tracks network flows, distinguishes new connections from
return traffic, and uses connection state to make stateful firewall decisions.

This session connected conntrack state with the
`RELATED,ESTABLISHED` firewall rule observed during the iptables session.

---

# Learning Summary

A firewall cannot correctly classify traffic by looking only at the direction
of an individual packet.

Both of the following packets enter the local host through the INPUT path:

```text
A response to a connection initiated by the local host

An unsolicited connection initiated by an external host
```

Connection tracking allows the kernel to distinguish them.

Conntrack records a network flow using information such as:

```text
Protocol
Source IP
Source port
Destination IP
Destination port
Reverse direction
Connection state
Timeout
```

For TCP and UDP, the central identity is commonly described as a five-tuple:

```text
Protocol
Source IP
Source port
Destination IP
Destination port
```

Conntrack stores both the original direction and the reply direction in one
entry.

```text
Original direction:
203.0.113.10:53000 → 198.51.100.20:443

Reply direction:
198.51.100.20:443 → 203.0.113.10:53000
```

When a reply packet matches the reverse direction of an existing flow, the
firewall can classify it as `ESTABLISHED` and accept it before the INPUT
chain's default `DROP` policy is reached.

Conntrack entries are temporary. Their timeout decreases while the flow is
inactive and may be refreshed when matching packets are observed. Expired
entries are removed so stale flows do not consume tracking resources or
interfere with later address and port reuse.

---

# Key Concepts

- Connection tracking
- conntrack
- Stateful firewall
- Five-tuple
- Original direction
- Reply direction
- Flow
- NEW
- ESTABLISHED
- RELATED
- INVALID
- ASSURED
- Timeout
- Reverse tuple
- `RELATED,ESTABLISHED`
- INPUT policy

---

# Practical Observations

## Observation 1 - One Entry Contains Both Directions

Prediction:

```text
A conntrack entry should contain both the original direction and the reply
direction of the same flow.
```

Observation:

```text
tcp 6 431986 ESTABLISHED
src=203.0.113.10 dst=198.51.100.20 sport=53000 dport=443
src=198.51.100.20 dst=203.0.113.10 sport=443 dport=53000
[ASSURED]
```

Explanation:

The first tuple represents the original direction.

```text
203.0.113.10:53000
        →
198.51.100.20:443
```

The second tuple represents the reply direction.

```text
198.51.100.20:443
        →
203.0.113.10:53000
```

The source and destination addresses and ports are reversed, but conntrack
records both directions as one flow.

---

## Observation 2 - Protocol and State Fields

The beginning of the observed entry was:

```text
tcp 6 431986 ESTABLISHED
```

Interpretation:

```text
tcp
= transport protocol

6
= TCP IP protocol number

431986
= remaining timeout in seconds

ESTABLISHED
= traffic belongs to an existing bidirectional flow
```

The entry also contained:

```text
[ASSURED]
```

This indicated that conntrack had observed sufficient bidirectional traffic to
treat the flow as confirmed rather than as an unverified first packet.

---

## Observation 3 - Timeout Decreased During Inactivity

Prediction:

```text
If no matching packet refreshes the flow, its timeout should decrease.
```

Observation:

```text
First query:
431995

Second query:
431984
```

The timeout decreased by approximately eleven seconds.

Explanation:

```text
Conntrack entry exists
        ↓
No matching packet activity
        ↓
Timeout decreases
        ↓
Timeout reaches zero
        ↓
Entry is removed
```

A new matching packet can refresh the timeout according to the protocol and
current connection state.

---

## Observation 4 - Why Entries Must Expire

Prediction:

```text
Keeping all conntrack entries permanently would preserve too much stale state.
```

Explanation:

Expired entries must be removed because:

- completed or inactive flows are no longer valid
- IP addresses and ports can later be reused
- old entries consume kernel memory and table capacity
- UDP has no connection-closing handshake to remove state explicitly
- stale state could cause later traffic to be classified incorrectly

A timeout is therefore a state-lifecycle mechanism, not a guaranteed period
during which traffic must be accepted.

---

## Observation 5 - Stateful INPUT Filtering

The observed firewall configuration used:

```text
INPUT policy DROP

RELATED,ESTABLISHED → ACCEPT
```

The resulting flow is:

```text
Local application starts an external connection
        ↓
OUTPUT permits the outbound packet
        ↓
Conntrack creates or updates a flow entry
        ↓
External response reaches INPUT
        ↓
Conntrack matches the reverse tuple
        ↓
Packet receives ct state ESTABLISHED
        ↓
RELATED,ESTABLISHED rule accepts it
        ↓
Default INPUT policy DROP is never reached
```

The default policy does not immediately drop every inbound packet. It applies
only when no earlier rule produces a final verdict.

---

# Connection States

## NEW

```text
A packet is attempting to begin a flow or belongs to a flow that has not yet
been confirmed as bidirectional.
```

Examples:

- the first TCP SYN sent by a client
- an unsolicited external TCP connection attempt
- the first observed UDP packet in a new flow

---

## ESTABLISHED

```text
A packet belongs to a flow for which traffic has been observed in both
directions.
```

Examples:

- a web server response to a locally initiated connection
- subsequent packets in an existing SSH session

---

## RELATED

```text
A packet begins a separate flow that conntrack associates with an existing
tracked connection.
```

The related flow is not the same five-tuple, but the kernel recognizes a
relationship with an existing connection.

---

## INVALID

```text
The packet cannot be associated with a valid tracked flow or does not make
sense for the expected protocol state.
```

Such packets are commonly dropped.

---

# Commands / Code

```bash
sudo conntrack -C
```

Shows the current number of conntrack entries.

---

```bash
sudo conntrack -L
```

Lists the currently tracked flows.

---

```bash
sudo conntrack -L | sed -n '1,20p'
```

Shows only the first twenty lines when the full conntrack table is large.

---

```bash
sudo conntrack -L -p tcp \
  --orig-src 203.0.113.10 \
  --orig-dst 198.51.100.20 \
  --sport 53000 \
  --dport 443
```

Filters the conntrack table for one TCP flow using its original direction.

Repeating the command after several seconds can reveal whether the timeout
decreases or is refreshed.

---

# Conntrack Mental Model

```text
First packet
        ↓
Conntrack creates an entry
        ↓
Original five-tuple recorded
        ↓
Reverse-direction packet observed
        ↓
Reply tuple matched to the same entry
        ↓
Flow classified as ESTABLISHED
        ↓
Firewall can permit response traffic
        ↓
Flow becomes inactive or closes
        ↓
Timeout expires
        ↓
Entry removed
```

---

# Connections

```text
Session 08 - NAT
        ↓
NAT relies on tracked address and port relationships

Session 10 - iptables Fundamentals
        ↓
RELATED,ESTABLISHED rules permit legitimate return traffic

Session 11 - nftables Overview
        ↓
Native nftables can match connection state with ct state

Session 12 - Connection Tracking
        ↓
The kernel supplies the flow state used by stateful filtering
```

Combined packet model:

```text
Outbound packet
        ↓
Routing and firewall processing
        ↓
Conntrack entry
        ↓
External network
        ↓
Reply packet
        ↓
Reverse tuple lookup
        ↓
ESTABLISHED classification
        ↓
Firewall ACCEPT
        ↓
Local application
```

---

# Common Misconceptions

- Incorrect: The INPUT `DROP` policy immediately drops every inbound packet.
- Correct: Rules are checked first. An `ESTABLISHED` response can be accepted
  before the default policy is reached.

---

- Incorrect: Conntrack stores only the original packet direction.
- Correct: One entry records both the original and reply directions.

---

- Incorrect: The timeout increases continuously with time.
- Correct: It decreases during inactivity and may be refreshed by matching
  traffic.

---

- Incorrect: Conntrack entries should be kept permanently to preserve
  connection integrity.
- Correct: Stale entries must expire to prevent incorrect state reuse and table
  exhaustion.

---

- Incorrect: Timeout is a guarantee that all matching packets will be allowed
  until it reaches zero.
- Correct: Timeout controls how long tracking state remains. Firewall rules
  still determine whether packets are accepted.

---

- Incorrect: Conntrack state and TCP protocol state are identical concepts.
- Correct: They are related, but conntrack presents the kernel's flow-tracking
  classification for firewall and NAT processing.

---

# Key Takeaways

- Conntrack lets Linux reason about flows rather than isolated packets.
- A flow is identified using protocol, addresses, ports, and reverse-direction
  information.
- One conntrack entry contains both the original and reply tuples.
- Return traffic can be classified as `ESTABLISHED`.
- Stateful firewall rules accept legitimate responses before a default `DROP`
  policy is reached.
- `[ASSURED]` indicates that a flow has been confirmed through bidirectional
  traffic.
- Conntrack entries are temporary and use timeouts to remove stale state.
- NAT and stateful firewalling both depend on connection-tracking information.

---

# Review Questions

### Q1. What problem does connection tracking solve for a firewall?

<details>
<summary>A</summary>

</details>

---

### Q2. Which five values commonly identify a TCP or UDP flow?

<details>
<summary>A</summary>

</details>

---

### Q3. Why does one conntrack entry contain two source and destination tuples?

<details>
<summary>A</summary>

</details>

---

### Q4. What does the `ESTABLISHED` conntrack state indicate?

<details>
<summary>A</summary>

</details>

---

### Q5. Why can a server response enter the INPUT path without being dropped by
the default INPUT policy?

<details>
<summary>A</summary>

</details>

---

### Q6. What does `[ASSURED]` indicate about an observed flow?

<details>
<summary>A</summary>

</details>

---

### Q7. Why did the observed timeout decrease between two conntrack queries?

<details>
<summary>A</summary>

</details>

---

### Q8. What can cause a conntrack timeout to be refreshed?

<details>
<summary>A</summary>

</details>

---

### Q9. Why would permanently retaining every conntrack entry cause problems?

<details>
<summary>A</summary>

</details>

---

### Q10. Complete the stateful firewall flow:

```text
Local host initiates connection
        ↓
?
        ↓
Server response reaches INPUT
        ↓
?
        ↓
ct state ESTABLISHED
        ↓
?
        ↓
Application receives response
```

<details>
<summary>A</summary>

</details>

---

# Next Session

```text
Next:
Phase 02
Session 13

Topic:
Network Namespaces
```
