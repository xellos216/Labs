# Session Archive Format

## Purpose

A completed session archive is the durable evidence record for one Curriculum
V2 session. It preserves what the learner predicted, performed, observed,
explained, and remained uncertain about.

Use one Korean Markdown file per completed session. Do not split a session into
`notes.md`, `notes_kor.md`, `QA.md`, or similar companion files.

## Creation Gate

Create the final archive only when:

1. The learner performed the primary work.
2. Commands, requests, captures, logs, or other relevant results were actually
   observed.
3. The roadmap's session completion criterion was reviewed.
4. The session was determined complete.
5. The learner explicitly requested or approved archive generation.

Do not create a draft, placeholder, or incomplete session archive. Do not
infer completion from chat history. If the session is incomplete or
inconclusive, keep the continuation in conversation or a bounded handoff
rather than creating progress evidence.

## Location and Filename

Store the archive under the roadmap that owns the session:

```text
<lifecycle>/<roadmap>/phaseXX/YY_session_title.md
```

Use lowercase English snake_case filenames and stable numeric identifiers:

```text
active/security_core/phase01/01_local_lab_boundaries_and_evidence.md
active/security_core/phase01/02_processes_and_procfs.md
```

Create `phaseXX/` only when its first session is actually complete and the
learner approves the archive. Do not create empty phase directories.

## Front Matter

Use lowercase keys and this structure:

```yaml
---
roadmap: security_core
phase: 01
session: 01
status: completed
date: 2026-08-17
scope: local_lab
---
```

The date above is an example. Use the real completion date. `roadmap`, `phase`,
and `session` must match the current roadmap. `scope` should identify the
authorized environment without exposing a private program or real target.

The only valid archive status is:

```text
completed
```

An incomplete record is not a session archive and must not be added as one.

## Required Korean Sections

Use these headings in this order:

```text
# 세션 제목
## 학습 목표
## 핵심 질문
## 사전 예측
## 실습 환경
## 수행한 작업
## 관찰된 증거
## 해석
## 재사용 가능한 Mental Model
## 실패, 한계 및 불확실성
## 복습 질문
```

The body and learner-facing narrative are Korean. Commands, code, protocol
names, APIs, errors, and technical identifiers retain their original form.

## Section Requirements

### Learning Objective (`학습 목표`)

State the completed roadmap session's intended capability and why it mattered.
Do not add objectives that were not part of the session.

### Core Question (`핵심 질문`)

Record the one central question or task used to evaluate completion.

### Prior Prediction (`사전 예측`)

Preserve the learner's actual prediction or hypothesis and its reasoning.
When no prediction was appropriate, state why rather than inventing one.

### Lab Environment (`실습 환경`)

Include only details required to understand or reproduce the observation.
Generalize or redact identifiers tied to the real host, network, account, or
authorized target.

### Work Performed (`수행한 작업`)

Summarize the actions the learner actually performed. Include commands or
requests only when they support reproducibility or explanation.

### Observed Evidence (`관찰된 증거`)

Record facts before interpretation. Include only the minimum relevant output,
packet fields, log excerpts, state changes, or browser behavior. Preserve the
link between an action and its observed result.

### Interpretation (`해석`)

Explain the mechanism supported by the evidence. Distinguish direct evidence,
inference, rejected alternatives, and remaining uncertainty.

### Reusable Mental Model (`재사용 가능한 Mental Model`)

Express the state transition, data path, trust boundary, or comparison that can
be applied to another case. Prefer a concise diagram when relationships would
otherwise be ambiguous.

### Failures, Limitations, and Uncertainty (`실패, 한계 및 불확실성`)

Preserve failed attempts and disconfirmed hypotheses when educationally
relevant. State what was not tested, what the evidence cannot prove, and which
environment differences could change the result.

### Review Questions (`복습 질문`)

Include concise questions that require the learner to reconstruct the evidence
and mechanism. Do not create a separate answer file. Populate answers only as
later learner-authored review within an explicitly scoped update.

## Evidence Rules

- Record only commands and results that were actually observed.
- Never convert expected output into observed output.
- Separate evidence from interpretation.
- Preserve provenance: action, observation point, and relevant environment.
- Include only the minimum raw output needed.
- Do not copy a raw chat transcript.
- Preserve failed hypotheses when they improve false-positive control.
- State limitations and uncertainty explicitly.
- Redact sensitive identifiers before writing public Markdown.

Detailed evidence may use a separate experiment record when
[Lab Experiment Template](LAB_EXPERIMENT_TEMPLATE.md) is appropriate. Link the
record from the session archive, but keep the completed session's integrated
explanation in the archive.

## Capstones and Phase Reviews

Use the same format for ordinary sessions, capstones, and phase reviews. A
capstone archive must show integrated evidence collection, reconstruction of a
state, path, or trust boundary, alternative-explanation checks, explanation,
limitations, and uncertainty.

Do not create a parallel capstone report or QA file solely because the session
is a review.

## Progress Evidence

Progress evidence is:

```text
committed session file
+
status: completed
```

The repository uses no separate progress checkboxes or progress database. The
template under `ops/templates/` is not progress evidence.

## Reusable Template

Copy [Korean Session Archive Template](../templates/session_archive_ko.md)
only after the creation gate is satisfied. Replace the example metadata and
instructional text with the actual completed session record, then validate it
under [Markdown Generation Policy](MARKDOWN_GENERATION_POLICY.md).
