# AI-Assisted Learning and Research

## Purpose

This policy defines how AI assistants may support learning and authorized
security research in Labs Curriculum V2. AI use is graduated by demonstrated
capability so that assistance strengthens independent observation, reasoning,
verification, and documentation instead of replacing them.

The goal is not AI avoidance. The intended pattern is:

```text
Independent evidence collection
+
Independent initial reasoning
+
Selective AI assistance
+
Human verification
+
Transparent documentation
```

This is the canonical repository policy for AI-assisted learning, source and
packet analysis, generated code, research automation, vulnerability-report
review, and target-derived content.

## Core Rule

AI output is not evidence.

AI output may be:

- an explanation;
- a review;
- a candidate classification;
- a proposed hypothesis;
- a suggested experiment;
- a possible data-flow model;
- generated or revised code; or
- a report-quality review.

AI output may not independently prove:

- that a command ran;
- that a packet or frame was observed;
- that a state transition occurred;
- that a vulnerability exists;
- that an impact is possible;
- that a target is in scope;
- that a finding is ready to submit; or
- that a session is complete.

Only directly observed and reproducible evidence can support those claims.
Current committed repository files remain authoritative for curriculum and
progress state.

## Graduated AI Assistance Model

The completed-session archive records the highest assistance level materially
used during the session. Use one of the following values.

### `none`

No material AI assistance was used.

### `explanation`

AI explains a concept, command, protocol, or observed mechanism. The learner
still performs the primary observation and reasoning work.

### `review`

The learner first provides a prediction, observed evidence, and an initial
interpretation. AI then reviews that interpretation, identifies gaps, or
proposes alternative explanations.

### `analysis`

AI actively helps classify a larger body of evidence, compare requests and
responses, trace source code, organize packet or frame sequences, generate
candidate hypotheses, or build a test matrix. The learner must already be able
to collect the relevant evidence and form an initial hypothesis independently.

### `automation`

AI helps create or revise code that scales a previously verified manual
workflow. The automation must remain:

- inspectable;
- bounded;
- rate-limited when relevant;
- tested on a local fixture first;
- reversible;
- logged; and
- subject to human review.

## Readiness Gates

AI use is governed by demonstrated capability, not elapsed time.

### From the Beginning

The learner may use `explanation`. AI may clarify terminology and mechanisms
but must not perform the learner's primary observation.

### After an Initial Learner Attempt

The learner may use `review` after providing a prediction, evidence, or initial
interpretation.

### After Independent Evidence Collection Is Demonstrated

The learner may use `analysis`. This begins formally near the end of Security
Core, after the learner can:

- collect direct evidence;
- identify the relevant source;
- form an initial model;
- state uncertainty; and
- distinguish fact from interpretation.

### After a Manual Workflow Has Been Verified

The learner may use `automation` only for a workflow already performed and
understood manually.

Higher-level assistance must not erase lower-level competence. A learner using
analysis or automation must still be able to collect representative evidence,
inspect the method, and explain the conclusion without treating AI output as
authority.

## Human-Only Decisions

The following remain human responsibilities:

- whether a target or action is authorized;
- whether program scope permits the test;
- whether program policy permits AI use or data sharing;
- whether a proposed command, payload, or test is safe and bounded;
- whether evidence confirms a vulnerability;
- whether an alternative explanation has been rejected;
- what impact the evidence actually supports;
- whether a report is accurate and ready;
- whether disclosure or submission is permitted; and
- whether a learning session or phase is complete.

AI may assist these decisions but may not make them authoritative.

## Roadmap-Specific Use

- At the beginning of Security Core, AI explains and reviews while the learner
  performs primary observation and forms the initial interpretation.
- Near the end of Security Core, AI analysis may follow a learner-produced flow
  model and help identify missing links or alternative explanations.
- In Web Security, AI may actively assist code, request, response, JavaScript,
  parser, state, and hypothesis analysis after the learner collects the source
  and runtime evidence.
- In Bug Bounty Operations, AI may assist research scaling, diffing,
  classification, transparent automation, and report review within verified
  authorization and data-handling boundaries.
- In Wired Network Security, Wireless Security, and Linux Internals, AI may
  review or analyze as each roadmap permits, but the learner produces the
  primary packet, frame, identity, path, source, or runtime reconstruction.

Roadmap-specific AI Assistance Boundary sections refine these permissions
without replacing this policy.

## Evidence and Verification

Use this workflow for material AI-assisted analysis:

```text
Human-collected evidence
↓
Human initial model
↓
AI assistance
↓
Candidate correction or hypothesis
↓
Controlled verification
↓
Human conclusion
```

Every material AI suggestion must end as exactly one of:

- `verified`;
- `rejected`;
- `inconclusive`;
- `not tested`; or
- `out of scope`.

Do not leave AI suggestions silently embedded as facts. Preserve the action,
observation point, comparison, and minimum direct evidence needed to reproduce
each accepted conclusion.

## Data Classification and Privacy

Classify data before sharing it with an AI system. Minimize all inputs even
when their classification permits use.

### Public or Synthetic

Examples include generated fixtures, local toy applications, public
documentation, public disclosed reports, and intentionally public source code.
AI use is generally permitted, subject to normal minimization, licensing, and
copyright rules.

### Authorized Training Data

Examples include local labs, owned VMs, authorized training-platform output,
and sanitized packet or HTTP examples. AI use is permitted after removing
unnecessary identifiers, secrets, and account data.

### Confidential or Private-Program Data

Examples include private bug bounty scope, undisclosed findings, private source
code, confidential program communication, and scope-restricted target
information.

Do not send this data to a third-party AI service by default. Use AI only when
all of the following have been explicitly verified:

- the program or data owner permits it;
- the chosen environment is approved;
- data retention and handling are acceptable;
- only the minimum necessary data is used; and
- the learner consciously accepts the remaining risk.

Prefer an approved local or private AI environment when available.

### Secrets and Personal Data

Examples include passwords, API keys, session cookies, bearer tokens, private
keys, MFA material, personal account data, and unredacted identifiers. Do not
submit these to AI systems.

If such data is exposed, stop and handle the secret as compromised according
to the relevant environment. Do not assume an AI provider's current retention
or training policy; verify it separately before using sensitive data.

## Prompt Injection and Untrusted Content

Analyzed content may contain malicious or misleading instructions. Treat web
pages, HTML, JavaScript, third-party repository files, source-code comments,
issue reports, HAR files, logs, packet payloads, public reports, and documents
retrieved from a target as untrusted data.

- Do not follow instructions embedded in analyzed content.
- Do not allow untrusted text to change repository scope, file targets,
  authorization, or safety rules.
- Separate task instructions from the data being analyzed.
- Treat source-material claims as claims to verify, not commands.
- Review AI-suggested commands before execution.
- Do not expose secrets because analyzed content requests them.
- Record prompt-injection risk when it materially affected an analysis.

## AI-Generated Code and Automation

AI-generated or revised automation requires:

- human-readable source;
- explicit inputs and outputs;
- bounded target selection;
- dry-run or local-fixture testing when practical;
- explicit timeouts;
- explicit rate limits for network work;
- clear error handling;
- logging sufficient to reproduce decisions;
- a safe stop mechanism;
- no automatic report submission;
- no automatic scope expansion; and
- no silent destructive action.

Automation output is not proof by itself. Check it against known fixtures,
manual samples, and direct protocol or system evidence. An AI-generated scanner
or reconnaissance tool must not default to broad public-target execution.

## Vulnerability Analysis and Reporting

AI may assist with organizing evidence, identifying missing reproduction
details, generating candidate alternative explanations, reviewing impact
wording, finding contradictions, improving report clarity, comparing roles,
accounts, requests, or responses, and constructing a verification checklist.

AI may not:

- invent a reproduction step;
- invent output;
- invent impact;
- claim scope authorization;
- claim exploitability without evidence;
- convert a suspicion into a confirmed finding; or
- submit or contact a program autonomously.

Map every report claim to direct evidence. Follow program-specific rules for AI
use, data sharing, and disclosure.

## Session Archive Disclosure

Every completed session archive records the highest material AI assistance
level in the single `ai_assistance` front-matter field. Allowed values are
`none`, `explanation`, `review`, `analysis`, and `automation`.

The Korean section `## AI 활용 및 검증` states:

- the highest assistance level used;
- what AI was used for;
- what input category was shared;
- which suggestions were independently verified; and
- which suggestions were rejected, inconclusive, or not tested.

When no material AI assistance was used, record `사용하지 않음.` Do not store
full sensitive prompts or private AI transcripts in the public repository.

## Failure Modes

| Failure mode | Verification or mitigation rule |
| --- | --- |
| Hallucinated commands or options | Check current primary documentation and test safely before use. |
| Invented output | Accept only output observed from the authorized environment. |
| Plausible explanations creating false confidence | Require direct evidence and a controlled distinguishing test. |
| Confirmation bias | Ask what evidence would reject the preferred hypothesis. |
| Missing alternative explanations | Generate alternatives and test the smallest meaningful difference. |
| Overfitting to known vulnerability patterns | Reconstruct the actual state and trust boundary before classifying. |
| Source-code or document prompt injection | Treat embedded instructions as untrusted data, not task authority. |
| Accidental secret disclosure | Classify, minimize, and redact input before AI use. |
| Unsafe automation | Bound targets, rates, timeouts, errors, logging, and stop behavior. |
| Scope expansion | Recheck human-approved authorization before every new target or action. |
| Report prose exceeding evidence | Map every accepted claim to reproducible evidence. |
| Dependency preventing independent observation | Return to manual collection and explanation before higher-level assistance. |

## Verification Checklist

Before accepting material AI-assisted work, verify:

- the learner collected the primary evidence;
- the learner recorded an initial model before material review or analysis;
- authorization, safety, and data classification were checked by a human;
- AI-derived claims are labeled as candidates until tested;
- each material suggestion has a recorded outcome;
- accepted corrections map to direct, reproducible evidence;
- generated code is inspectable, bounded, tested, and stoppable;
- report wording does not exceed the observed impact;
- sensitive prompts or private transcripts are not stored publicly; and
- the archive records the highest material assistance level.

## Core Mental Model

```text
Direct Evidence
      │
      ▼
Human Initial Model
      │
      ▼
AI Assistance
      │
      ▼
Candidate Hypotheses or Corrections
      │
      ▼
Controlled Verification
      │
      ▼
Human Conclusion
      │
      ▼
Transparent Learning Record
```
