# Labs Curriculum V2

Labs is a public, evidence-based curriculum for authorized security learning
and research. It connects Unix/Linux observation, network protocol reasoning,
web and API security, bug bounty research operations, wired network security,
and Wi-Fi security through reproducible investigation.

The repository is a learning record and curriculum specification, not a
commercial course or a substitute for direct practice.

## Current Status

```text
Curriculum version: V2
Active roadmap: Security Core
Current phase: Phase 01 — Unix/Linux Observation
Completed sessions: none
Next session: Session 01 — Local Lab Boundaries and Evidence
```

Curriculum V2 begins from zero. No earlier session work counts as a V2
prerequisite or completed-session credit.

## Evidence-Based Learning Cycle

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

Conclusions must remain traceable to observed evidence. The curriculum
prioritizes protocol and trust-boundary reasoning, hypothesis testing,
false-positive rejection, and durable mental models over tool collection or
memorized procedures.

## Graduated AI Assistance

Labs uses a [graduated AI assistance
model](ops/docs/AI_ASSISTED_LEARNING_AND_RESEARCH.md). Learners collect direct
evidence and form an initial model before AI progresses from explanation and
review to active analysis or bounded automation. AI output is never evidence,
and every material suggestion requires human verification. Sensitive research
data follows the policy's strict handling and minimization rules.

## Roadmap Lifecycle

- [`active/`](active/README.md) contains the roadmap currently being
  practiced. The initial active roadmap is [Security
  Core](active/security_core/README.md).
- [`backlog/`](backlog/README.md) contains complete roadmap specifications
  awaiting activation.
- [`archive/`](archive/README.md) contains roadmaps whose completion criteria
  have been met. No V2 roadmap is initially archived.
- [`ops/`](ops/README.md) contains governance, formats, templates, and
  operational documentation.

See the [Roadmap Index](ops/docs/ROADMAP_INDEX.md) for canonical paths,
activation relationships, and current progress.

## Language Policy

Repository governance and roadmap specifications are written in English.
Completed session archives are written in Korean so that learner predictions,
observations, explanations, failures, and uncertainty remain natural and
precise. Commands, protocol names, APIs, and technical identifiers retain
their original form.

## Source of Truth

Current committed repository files are canonical. Observed system output,
focused branch or diff evidence, explicit handoffs, Project Sources, and chat
context cannot silently replace committed curriculum or progress records.
Project Sources, AI output, and AI memory are working context, not
authoritative state.

## Authorization and Publication Boundary

Practical work must stay inside local labs, owned systems, isolated
environments, authorized training platforms, or other explicitly authorized
scope. Private program data, undisclosed findings, live credentials, tokens,
personal account data, unredacted packet captures, confidential source, and
unsafe real-target identifiers must not be committed here. Private research
evidence belongs in a separate private workspace.

The public repository may contain local-lab results, authorized
training-platform results, public disclosed-report analysis, sanitized
mechanism notes, redacted examples, and reusable research workflows.

## License

Review [License and Scope](LICENSE.md) and [Third-Party
Notices](THIRD_PARTY_NOTICES.md) before copying or adapting repository
material. Canonical third-party legal text is stored under `LICENSES/`.
