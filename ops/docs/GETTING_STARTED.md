# Getting Started with Labs Curriculum V2

## What This Repository Is

Labs is a public curriculum and learning record for authorized security
investigation. It develops evidence-based reasoning across Unix/Linux
observation, network protocols, web and API security, bug bounty operations,
wired networks, Wi-Fi, and optional Linux internals.

It is not a blank template, a completed commercial course, or a substitute for
performing the work in a real authorized environment.

## Current Starting Point

```text
Curriculum version: V2
Active roadmap: Security Core
Current phase: Phase 01 — Unix/Linux Observation
Completed sessions: none
Next session: Session 01 — Local Lab Boundaries and Evidence
```

Begin with the [Roadmap Index](ROADMAP_INDEX.md), then read [Security
Core](../../active/security_core/README.md) and [Labs Session
Rules](LABS_SESSION_RULES.md). Do not treat earlier Git history, cached context,
or old branch content as V2 completion credit.

## Obtain the Repository

Clone the public repository when you need a local reference copy:

```bash
git clone https://github.com/xellos216/labs.git
cd labs
```

Inspect the current branch and commit before continuing curriculum work:

```bash
git branch --show-current
git log -1 --oneline
git status --short
```

Current committed files are canonical. A local uncommitted change, focused
branch diff, handoff, Project Source, or chat must be identified explicitly
before it can inform the current task.

## Understand the Repository

- `active/` contains the roadmap currently being practiced.
- `backlog/` contains complete roadmap specifications awaiting activation.
- `archive/` contains roadmaps whose full completion criteria were met.
- `ops/docs/` defines governance, formats, source precedence, and session rules.
- `ops/templates/` contains reusable documentation templates.

Normal operation allows one primary roadmap and zero or one bounded secondary
roadmap. Linux Internals can be activated temporarily when an investigation
reaches an operating-system mechanism boundary.

## Prepare an Authorized Lab

Before a practical session, identify:

- systems and accounts you own or are explicitly authorized to test
- the local, isolated, or training-platform scope
- data-handling and publication constraints
- the observation point and required tool capability
- stop conditions for unexpected reachability or sensitive data

Use local services, VMs, containers, network namespaces, owned networks,
isolated access points, intentionally vulnerable applications, or authorized
training platforms. Do not infer authorization from technical reachability.

## Run a Session

Use this flow:

```text
Minimal Setup
↓
Question or Task
↓
Learner Prediction
↓
Learner Performs Primary Work
↓
Observed Output
↓
Review and Minimal Correction
↓
Mechanism Explanation
↓
Completion Decision
↓
Korean Session Archive
```

The learner performs the primary terminal or browser work. A normal session
has one central question, no more than five core tasks, and one explicit
completion criterion. Keep evidence separate from interpretation and investigate
plausible alternatives before accepting a conclusion.

Do not change phase order, add unrelated work, or declare completion because a
conversation was long. Phase transitions require explicit approval.

## Record a Completed Session

After the learner and assistant review completion, the learner may request or
approve a final archive. Follow [Session Archive
Format](SESSION_ARCHIVE_FORMAT.md) and the [Korean
template](../templates/session_archive_ko.md).

Create one Korean Markdown file under the owning roadmap's `phaseXX/`
directory. Record only actual commands and observations, use the real
completion date, redact sensitive identifiers, and preserve limitations. Do
not create an incomplete session file or a separate notes, QA, or progress
file.

## Git Workflow

Use a short-lived branch for bounded work:

```text
learn/<roadmap>_pXX_sYY
fix/<bounded_issue>
refactor/<bounded_refactor>
```

Inspect status and diffs before and after changes. Do not use long-lived
roadmap branches, rewrite history, or discard unrelated work. Committed session
files with `status: completed` are progress evidence; there is no separate
progress database.

## Public Repository Safety

Do not commit private program data, undisclosed findings, restricted target
information, credentials, tokens, personal account data, unredacted captures,
confidential source, or unsafe target identifiers. Store real private-program
evidence in a separate private workspace.

Follow [Markdown Generation Policy](MARKDOWN_GENERATION_POLICY.md) before
publishing learner records. Visual media requires manual pixel and metadata
review in addition to text searches.

## Optional AI Assistant

Labs does not require an AI assistant. If you use ChatGPT or another assistant,
read [Optional ChatGPT Setup](CHATGPT_SETUP.md). The assistant can ask
questions, review evidence, explain mechanisms, and draft an approved archive;
it cannot substitute generated text for observed system behavior.
