# Unix/Linux Command-Line Foundations Roadmap

## Objective

Build practical command-line foundations for answering bounded questions about
a Unix/Linux system.

After completing this roadmap, the learner should be able to:

- select a suitable Unix/Linux observation tool
- identify and interpret its important output
- reduce output with a short, composable pipeline
- explain what the observed output directly supports
- recognize when deeper diagnosis belongs in another roadmap

This roadmap teaches foundational tool use. It does not attempt to become a
course in deep `awk` programming, shell application development, Linux server
administration, network protocol theory, or production incident response.
Those topics are delegated to the Server Administration, Networking, or a
future advanced `awk`/shell-programming roadmap.

# Philosophy

- understanding over memorization
- composability over monolithic tools
- stream-based thinking
- practical command use
- observability first
- small tools for bounded questions
- simple solutions when simple solutions are sufficient
- explicit scope boundaries
- evidence before interpretation

# Learning Method

Unix sessions use the learner-first Unix Task-Solving Mode defined in
[`ops/docs/LABS_SESSION_RULES.md`](../../ops/docs/LABS_SESSION_RULES.md).

```text
Setup
↓
Observe
↓
Learner Solves
↓
Review and Correction
↓
Explain the Result
↓
notes.md only when requested
```

The learner performs the primary terminal work. Review focuses on the observed
result, the smallest necessary correction, and the reusable command pattern.

# Lab Environment

- Arch Linux
- zsh
- tmux
- neovim
- CLI-first workflow

Experiments should remain local, reproducible, observable, reversible, and
appropriately scoped. Use sample files, temporary directories, loopback
services, or other controlled targets when practical.

# Typical Phase Structure

Future Unix phases use this progression:

```text
Observe the Source
↓
Use the Primary Tool
↓
Filter or Select
↓
Compose a Short Pipeline
↓
Apply or Review
```

Ordinary future Unix sessions are designed for approximately 20 minutes of
planned work. This is a workload-design target, not a hard runtime cutoff;
unexpected troubleshooting may take longer. Troubleshooting must not silently
expand the planned curriculum or create additional core Tasks.

Ordinary and review sessions contain at most five core Tasks. Setup,
verification, and Wrap-up are not core Tasks. The final listed review session
is the planned phase endpoint. Follow-up recommendations do not silently create
additional sessions. Substantive expansion requires an explicit roadmap
revision and user approval, and every phase transition requires explicit user
approval.

Phase 01 and Phase 02 predate this bounded operating contract. Their published
session history and completion points are preserved rather than retroactively
rewritten.

# Historical Session Label Policy

Historical session labels below are retrospective descriptions derived from
the committed session records. They provide roadmap navigation and do not
rename, rewrite, or claim to reproduce the original planning history of those
archives.

# Phase 01 — Text Processing Fundamentals

## Goal

Build a practical model of text as records and fields that can be filtered,
transformed, grouped, counted, and ranked with small Unix tools.

## Outcomes

After completing this phase, the learner should be able to:

- distinguish line filtering from field-aware filtering
- extract delimited or whitespace-separated fields
- perform simple numeric and string comparisons
- count and deduplicate values with `sort` and `uniq`
- preserve a sort key until final output projection
- compose short filter, extract, count, and ranking pipelines

## Typical Tools

- `grep`
- `cut`
- `awk`
- `sed`
- `sort`
- `uniq`
- `tr`
- pipes

## Example Labs

- filter event records and count values by user
- extract fields from colon-separated records
- rank HTTP statuses, process metrics, or filesystem usage
- compare line matching with field-aware selection

## Sessions

| Session | Retrospective label |
| ------- | ------------------- |
| 01 | CSV Event Filtering and Counting |
| 02 | Request Method and Latency Filtering |
| 03 | Multi-File Log Aggregation |
| 04 | Directory-Scoped Log Filtering |
| 05 | Recursive Web Log Processing |
| 06 | Safe Multi-File Access Log Analysis |
| 07 | Delimited User Records |
| 08 | Sales Record Filtering and Deduplication |
| 09 | Key-Value Metric Extraction |
| 10 | Authentication Log Filtering |
| 11 | Process Record Inspection |
| 12 | Connection State Records |
| 13 | Disk Usage Ranking |
| 14 | Service Log Severity |
| 15 | Filesystem Usage Ranking |
| 16 | Process CPU and Memory Ranking |
| 17 | Socket State Ranking |
| 18 | HTTP Error Path Analysis |
| 19 | Authentication Event Classification |
| 20 | Service State Records |
| 21 | Monitoring Event Records |
| 22 | Runtime Process Roles |
| 23 | Capacity Thresholds |
| 24 | Listening Port Records |
| 25 | Phase01 Final Review |

## Completion Point

Phase 01 is historical completed work. Session 25 is its recorded completion
point. Some historical sessions exceeded the newly adopted complexity or Task
limits; those records remain unchanged and do not define routine future
requirements.

# Phase 02 — Filesystem & Multi-File Processing

## Goal

Treat paths, file contents, and metadata as distinct data sources while
processing multiple files safely and observably.

## Outcomes

After completing this phase, the learner should be able to:

- discover files recursively with explicit selection criteria
- distinguish pathname transport from content processing
- inspect and rank basic file metadata
- handle filenames safely across command boundaries
- compare shell globbing with `find` traversal
- apply and verify a narrowly scoped recursive replacement

## Typical Tools

- `find`
- `xargs`
- shell globbing
- `grep`
- `stat`
- `wc`
- `sort`
- `sed`

## Example Labs

- count matching events across several log files
- compare files by size or match count
- observe NUL-delimited filename transport
- inspect, change, and verify selected configuration files

## Sessions

| Session | Retrospective label |
| ------- | ------------------- |
| 01 | Recursive File Discovery |
| 02 | File Metadata and Size |
| 03 | Per-File Status Analysis |
| 04 | Multi-File Status Aggregation |
| 05 | Layered File and Content Filtering |
| 06 | Per-File Metric Ranking |
| 07 | Metadata Ranking |
| 08 | File Classification by Extension |
| 09 | Filesystem Summary Reporting |
| 10 | Safe Filename Transport |
| 11 | Executing Commands with find |
| 12 | Shell Globbing vs find |
| 13 | Controlled Recursive Replacement |
| 14 | Phase02 Integrated Practice |
| 15 | Phase02 Final Review |

## Completion Point

Phase 02 is historical completed work. Session 15 is its recorded completion
point. Advanced `awk` aggregation and integrated shell reports in the
historical records are preserved learning history, not routine prerequisites
for future Unix sessions.

# Phase 03 — Log Analysis & Observability

## Goal

Select, follow, and reduce relevant events from static logs and the system
journal without requiring a complex parser.

## Outcomes

After completing this phase, the learner should be able to:

- select relevant events from a static log or journal
- use basic unit, priority, time, tag, or pattern filters
- follow a live log stream and identify new events
- perform one simple field extraction or count
- distinguish observed evidence from a causal interpretation
- recognize when log analysis requires deeper server expertise

## Typical Tools

- `grep`
- simple `awk`
- `sort`
- `uniq`
- `wc`
- `tail`
- `journalctl`
- `logger`

## Example Labs

- count HTTP statuses from a static access log
- select authentication events by a stable signature
- filter journal entries by unit, priority, tag, or time
- emit a local message and observe it in a live journal stream

## Sessions

| Session | Label | Status |
| ------- | ----- | ------ |
| 01 | HTTP Access Log Analysis | Historical |
| 02 | Authentication Event Analysis | Historical |
| 03 | Structured Service Metric Analysis | Historical |
| 04 | Incident Timeline Correlation | Historical |
| 05 | Journal-Style Multi-Service Failure Observation | Historical |
| 06 | journalctl Fundamentals | Planned |
| 07 | Live Logs with logger and journalctl -f | Planned |
| 08 | Phase Review: Selecting and Following Logs | Planned |

## Completion Point

Session 08 is the planned Phase 03 endpoint. The phase is complete when the
learner can select and follow relevant log events, apply one simple extraction
or count, and explain the observed evidence without relying on a complex
parser.

Deeper journald storage architecture, rsyslog routing, rotation policy,
systemd failure diagnosis, production incident workflows, and complex
cross-service causal analysis belong in Server Administration or another
dedicated roadmap.

# Phase 04 — Process & Service Inspection

## Goal

Observe running processes and service state, then connect service-level output
to process evidence.

## Outcomes

After completing this phase, the learner should be able to:

- identify PID, PPID, state, and command fields in process output
- select processes by name or identifier
- explain a parent-and-child process relationship
- inspect a bounded CPU or memory snapshot
- observe the effect of a basic signal
- compare service status with process evidence

## Typical Tools

- `ps`
- `pgrep`
- `pidof`
- `pstree`
- `top`
- `kill`
- `systemctl`

## Example Labs

- locate one process and trace its parent
- compare a `systemctl status` result with `ps` output
- send a basic signal to a controlled local process
- rank a small process snapshot by CPU or memory use

## Sessions

| Session | Title |
| ------- | ----- |
| 01 | Process Listings with ps |
| 02 | Selecting Processes with pgrep and pidof |
| 03 | Parent and Child Processes |
| 04 | Resource Snapshots with top and ps --sort |
| 05 | Signals and Basic Process Control |
| 06 | Observing Service State with systemctl |
| 07 | Phase Review: From Service State to Process Evidence |

## Completion Point

Session 07 is the planned Phase 04 endpoint. Unit-file design, dependencies,
startup ordering, boot targets, restart policy, and methodical service recovery
are delegated to Server Administration.

# Phase 05 — Permissions & Ownership

## Goal

Explain local access results using ownership, mode bits, directory traversal,
process identity, and default permissions.

## Outcomes

After completing this phase, the learner should be able to:

- interpret owner, group, and mode output
- distinguish file permissions from directory permissions
- compare symbolic and numeric mode changes
- observe ownership and group membership
- predict and verify a basic `umask` result
- recognize special permission bits
- compare current and `sudo`-created process identity

## Typical Tools

- `ls`
- `stat`
- `id`
- `groups`
- `chmod`
- `chown`
- `chgrp`
- `umask`
- `sudo`

## Example Labs

- predict whether a controlled file operation will succeed
- compare file and directory traversal requirements
- observe default modes before and after changing `umask`
- compare `whoami`, `id`, `sudo whoami`, and `sudo id`

## Sessions

| Session | Title |
| ------- | ----- |
| 01 | Reading Ownership and Permission Modes |
| 02 | File Permissions vs Directory Permissions |
| 03 | Symbolic chmod |
| 04 | Numeric chmod |
| 05 | Ownership and Group Observation |
| 06 | umask and Default Modes |
| 07 | Recognizing Sticky, setuid, and setgid Bits |
| 08 | Observing Effective Identity with sudo |
| 09 | Phase Review: Diagnosing a Local Permission Denial |

## Completion Point

Session 09 is the planned Phase 05 endpoint. Session 08 remains limited to the
mental model:

```text
current process identity
→ sudo-created process identity
```

PAM, sudoers policy, privilege-delegation design, password policy, account
lifecycle, and server hardening are delegated to Server Administration.

# Phase 06 — Networking & Socket Observation

## Goal

Observe local network configuration, reachability, sockets, name resolution,
and HTTP responses from the command line.

## Outcomes

After completing this phase, the learner should be able to:

- identify local interfaces and addresses
- locate the default route
- distinguish listening sockets from established connections
- observe reachability and path output
- inspect basic DNS query results
- inspect HTTP response headers and status
- recognize when a question requires deeper network reasoning

## Typical Tools

- `ip`
- `ss`
- `ping`
- `traceroute`
- `dig`
- `curl`

## Example Labs

- identify the interface and address used by a local system
- find a listening socket and its associated process when permitted
- compare a DNS answer with an HTTP request target
- inspect a route, socket, and response for one bounded endpoint

## Sessions

| Session | Title |
| ------- | ----- |
| 01 | Interfaces and Addresses with ip |
| 02 | Routes and the Default Route |
| 03 | Listening Sockets with ss |
| 04 | Established Connections with ss |
| 05 | Reachability and Path Observation |
| 06 | DNS Output with dig |
| 07 | HTTP Response Observation with curl |
| 08 | Phase Review: Name, Route, Socket, Response |

## Completion Point

Session 08 is the planned Phase 06 endpoint. Protocol theory, routing
decisions, Linux packet flow, NAT, firewalling, namespaces, packet capture, and
security networking are delegated to the Networking roadmap.

# Phase 07 — Data Streams & Bounded Automation

## Goal

Understand shell data flow and construct short, observable command
combinations without turning the phase into a shell-programming course.

## Outcomes

After completing this phase, the learner should be able to:

- distinguish standard input, standard output, and standard error
- compare overwrite, append, and error redirection
- duplicate a stream with `tee`
- use a one-value variable or command substitution
- inspect and set a command environment
- perform bounded repetition over explicit inputs
- explain the responsibility of each stage in a short pipeline

## Typical Tools

- standard input, standard output, and standard error
- redirection operators
- `tee`
- `wc`
- `head`
- `tail`
- `env`
- `printenv`
- one-value variables
- command substitution
- short bounded loops

## Example Labs

- preserve output while passing it to another command
- separate normal output from an error stream
- compare an inherited environment with a one-command override
- repeat one observation command over a short explicit list

## Sessions

| Session | Title |
| ------- | ----- |
| 01 | Standard Input, Output, and Error |
| 02 | Redirecting and Appending Output |
| 03 | Duplicating Streams with tee |
| 04 | One-Value Variables and Command Substitution |
| 05 | Inspecting and Setting Command Environments |
| 06 | Bounded Repetition over Explicit Inputs |
| 07 | Phase Review: Building a Short Observable Pipeline |

## Completion Point

Session 07 is the planned Phase 07 endpoint. Functions, arrays, traps,
argument parsing, reusable applications, large loops, reporting frameworks,
and stateful `awk` programs are delegated to a future advanced shell/`awk`
topic.

# Phase 08 — Real System Workflows

## Goal

Apply earlier tools to bounded real-system questions without turning a session
into production remediation or a large integrated report.

## Outcomes

After completing this phase, the learner should be able to:

- choose the relevant observation source for a bounded question
- collect a small number of direct facts
- reduce evidence with a short pipeline when useful
- distinguish observation from explanation or speculation
- identify when deeper diagnosis belongs in another roadmap

## Typical Tools

- text and file tools from Phase 01 and Phase 02
- log tools from Phase 03
- process and service tools from Phase 04
- permission tools from Phase 05
- network observation tools from Phase 06
- stream tools from Phase 07

## Example Labs

- narrow a growing log to the relevant events
- compare process and service snapshots
- inspect a local permission denial
- connect an endpoint observation to a route and socket
- narrow a resource snapshot before escalating the diagnosis

## Sessions

| Session | Title |
| ------- | ----- |
| 01 | File and Log Triage |
| 02 | Process and Service Snapshot |
| 03 | Local Permission Failure |
| 04 | Endpoint and Socket Observation |
| 05 | Resource Snapshot and Narrowing |
| 06 | Final Review: Evidence Before Action |

## Completion Point

Session 06 is the planned Phase 08 and roadmap endpoint. Completion requires
the learner to select an observation source, collect a bounded set of facts,
apply a short pipeline when useful, separate observation from interpretation,
and identify the appropriate deeper roadmap. It does not require a final
multi-variable `printf` report.

Production remediation, service recovery architecture, packet reasoning,
security incident response, and automation frameworks remain delegated to
their dedicated roadmaps.

# Final Outcome

After completing the roadmap, the learner should be able to:

- identify a suitable Unix/Linux tool for a bounded question
- inspect and interpret its important output
- compose short pipelines whose stages have distinct responsibilities
- preserve the distinction between observation and inference
- avoid unnecessary parser or script complexity
- recognize when deeper Server, Networking, or programming study is required

# Core Mental Model

```text
System Question
↓
Select Observable Source
↓
Filter
↓
Extract
↓
Compose
↓
Explain Evidence
↓
Delegate Deeper Diagnosis When Needed
```
