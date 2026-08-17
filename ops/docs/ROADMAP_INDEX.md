# Roadmap Index

## Purpose

This is the canonical navigation and lifecycle index for Labs Curriculum V2.
Roadmap content is defined by each roadmap README; progress is determined from
committed completed-session files.

## Roadmaps

| Location | Roadmap | Primary Focus | Status |
| --- | --- | --- | --- |
| `active/security_core/` | Security Core | Linux observation, protocol reasoning, HTTP/browser foundations | Active |
| `backlog/web_security/` | Web Security | Web and API vulnerability mechanisms | Backlog |
| `backlog/bug_bounty/` | Bug Bounty Operations | Scope, attack surface, validation, evidence, reporting | Backlog |
| `backlog/wired_network/` | Wired Network Security | LAN, segmentation, enterprise protocols and identity | Backlog |
| `backlog/wireless_security/` | Wireless Security | 802.11 and Wi-Fi security | Backlog |
| `backlog/linux_internals/` | Linux Internals | Optional OS-mechanism support | Backlog |

## Lifecycle Definitions

- **Active** — currently practiced under `active/`; normal operation permits
  one primary roadmap and zero or one secondary roadmap.
- **Backlog** — a complete curriculum specification under `backlog/` that is
  not currently active.
- **Archived** — a roadmap under `archive/` whose full completion criteria are
  supported by committed completed-session evidence.

Repository location and roadmap status must agree. Move the whole roadmap
directory and update this index when lifecycle state changes.

## Initial V2 Baseline

```text
Curriculum version: V2
Initial active roadmap: Security Core
First phase: Phase 01 — Unix/Linux Observation
Baseline completed sessions: none
First session: P01-S01 — Local Lab Boundaries and Evidence
```

This immutable block records the V2 starting state. Do not update it as
sessions are completed; determine current progress from committed session
archives and the ordered roadmap specification.

Initial roadmap allocation:

```text
Primary: Security Core
Secondary: none
```

No earlier curriculum session, archive, numbering, or completion claim counts
as V2 progress.

## Primary Progression

```text
Security Core
      │
      ├───────────────┐
      ▼               ▼
Web Security     Bug Bounty Operations
      │
      ▼
Wired Network Security
      │
      ▼
Wireless Security
```

This expresses activation relationships, not a fixed calendar. Web Security
and Bug Bounty Operations may reinforce one another while retaining separate
canonical responsibilities: mechanism reasoning belongs to Web Security;
authorized research selection, validation, evidence, and reporting belong to
Bug Bounty Operations.

## Cross-Cutting AI Assistance

AI use is governed by [AI-Assisted Learning and
Research](AI_ASSISTED_LEARNING_AND_RESEARCH.md) and graduated by demonstrated
competence. AI is integrated into Security Core, Web Security, and Bug Bounty
Operations rather than represented as a separate roadmap. Roadmap-specific
boundaries define permitted assistance while direct evidence and human
verification remain authoritative.

## Optional Linux Internals Support Path

```text
A security investigation reaches an OS mechanism boundary
        ↓
Activate the relevant Linux Internals phase
        ↓
Observe and explain the required mechanism
        ↓
Return to the original research track
```

Linux Internals is a bounded optional secondary roadmap, not a fixed
prerequisite for the specialized security roadmaps.

## Active-Roadmap Limit

Normal operation permits:

```text
one primary roadmap
+
zero or one secondary roadmap
```

Activate a secondary roadmap only for a defined dependency or coherent support
workstream. Record the activation decision and return point. Do not create
parallel active tracks merely because their topics are interesting.

## Suggested Long-Term Priority

This allocation guides emphasis and is not a fixed schedule:

| Area | Suggested emphasis |
| --- | ---: |
| Web Security and Bug Bounty Operations | 60% |
| Wired Network Security | 20% |
| Wireless Security | 15% |
| Linux Internals | 5% |

## Canonical Paths

```text
active/security_core/README.md
backlog/web_security/README.md
backlog/bug_bounty/README.md
backlog/wired_network/README.md
backlog/wireless_security/README.md
backlog/linux_internals/README.md
```

If a path is missing or conflicts with this index, report and resolve the
repository inconsistency instead of inventing a replacement sequence.

## Progress Determination

Determine progress from:

1. the current committed roadmap README
2. committed session files inside that roadmap
3. `status: completed` in each completed session's front matter

Do not infer progress from chat history, Project Sources, directory names,
templates, uncommitted drafts, tool-platform completion, or a handoff alone.
Curriculum V2 has no separate progress database and no progress checkboxes.
