# Lab Experiment Format

## Purpose

This document defines a generic record for a detailed controlled experiment.
Use it when a session archive alone cannot preserve the reproduction steps,
environment, evidence provenance, or analysis of an unexpected result.

An experiment record supports a session. It does not independently mark a
session complete or replace the Korean completed-session archive.

## When to Use a Separate Record

Use a separate experiment record when the work needs one or more of:

- detailed reproduction steps
- environment-specific setup
- several observation points or evidence files
- controlled comparison of competing hypotheses
- investigation of a failed or inconclusive result
- safe reuse outside the original session

Keep a small experiment inside the session archive when a separate file would
add no useful evidence or reproducibility.

## Language and Filename

This specification is English. Write learner predictions, observations,
interpretations, failures, and uncertainty in Korean. Preserve commands, code,
protocol names, APIs, and technical identifiers in their original form.

Use an English snake_case filename that identifies the experiment without
exposing a private target:

```text
tcp_state_transition_capture.md
cross_role_response_comparison.md
```

## Metadata

Use lowercase front-matter keys:

```yaml
---
roadmap: security_core
phase: "02"
session: "05"
experiment: tcp_state_transition_capture
status: completed
date: "2026-08-17"
scope: isolated_lab
---
```

Use the real date and matching roadmap identifiers. Valid experiment status
values are listed below. Keep `phase`, `session`, and `date` quoted so YAML
parsers preserve leading zeros and do not coerce dates.

```text
completed
inconclusive
failed
```

`failed` means the procedure could not be completed. A completed procedure
that disproved the hypothesis is `completed`, not `failed`.

## Required Content

The experiment record should contain these learner-facing sections in Korean:

```text
# 실험 제목
## 실험 목적
## 핵심 가설
## 실습 환경과 범위
## 통제 조건
## 수행 절차
## 관찰된 증거
## 대안 설명 검토
## 해석
## 실패, 한계 및 불확실성
## 재현 조건
## 세션과의 관계
```

## Content Rules

### Experiment Purpose and Hypothesis

State one question and the prediction made before observation. Explain which
evidence would support, reject, or leave the hypothesis inconclusive.

### Environment, Scope, and Controls

Record only the environment details needed for reproduction. State the
authorized boundary, variables held constant, comparison cases, capture points,
and relevant stop conditions.

### Procedure

Describe reproducible actions in order and explain their intent. Do not report
an action as completed unless it was actually performed. Keep troubleshooting
that materially diverged from the experiment in a separate workstream.

### Observed Evidence

Record facts without mechanism claims. Tie each excerpt or evidence path to its
action and observation point. Include only the minimum raw data required to
support later analysis.

### Alternative Explanations and Interpretation

Compare the evidence with plausible alternative mechanisms. State which checks
rejected an alternative and which uncertainty remains. Explain the resulting
state, data path, or trust-boundary model.

### Failures, Limits, and Reproduction

Preserve useful failed attempts and state what the experiment cannot establish.
List the minimum conditions another authorized learner would need to reproduce
the result. Do not use progress checkboxes.

### Relationship to the Session

Identify the owning roadmap session and explain what completion evidence this
experiment supplies. Link the final completed-session archive when available.

## Evidence Storage

Prefer small sanitized text excerpts. When separate evidence files are
necessary, use repository-relative links and explain what each file proves.
Do not commit private program data, undisclosed findings, live credentials,
personal account data, unredacted captures, confidential source, or unsafe
real-target identifiers.

Real private-program evidence belongs in a separate private workspace. A
public experiment record may refer only to sanitized conclusions and
publication-safe evidence.

## Validation

Before accepting a record, verify that:

- the experiment was actually performed
- status matches the observed outcome
- action and evidence provenance are clear
- interpretation is separate from observation
- alternatives, limitations, and uncertainty are explicit
- identifiers and evidence are publication-safe
- links resolve locally
- the record does not claim session completion by itself

Follow [Session Archive Format](SESSION_ARCHIVE_FORMAT.md) for the final session
record and [Markdown Generation Policy](MARKDOWN_GENERATION_POLICY.md) for
formatting, redaction, media review, and validation.
