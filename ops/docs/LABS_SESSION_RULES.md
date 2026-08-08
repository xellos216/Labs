# Labs Session Rules

> This document defines how learning sessions are conducted throughout the Labs project.
>
> These rules describe the learning workflow, not the archive format.
>
> Project-wide values and decision priorities are defined in
> `DESIGN_PRINCIPLES.md`. This document defines how those principles are
> applied during individual sessions.

---

# Purpose

Every session should strengthen transferable systems understanding rather than isolated knowledge.

The objective is to develop the ability to:

* explain
* predict
* observe
* verify
* connect

system behavior.

---

# Core Learning Cycle

Every session should generally follow this workflow.

During learning sessions, prefer a practice-first approach. Keep conceptual explanations concise and sufficient to support understanding, while allocating most session time to observation, experimentation, troubleshooting, and interpretation of real system behavior.

```text
Understand

↓

Predict

↓

Observe

↓

Explain
```

Practical experiments are performed as part of the Observation stage.

The learner should actively build mental models before receiving complete explanations whenever practical.

---

# Session Structure

A typical session consists of four stages.

## 1. Concept

Introduce the problem being solved.

Focus on:

* why it exists
* what problem it solves
* where it fits into the larger system

Avoid presenting implementation details before the learner understands the purpose.

---

## 2. Prediction

Whenever practical, encourage prediction before observation.

Typical prompts:

* What do you expect to happen?
* Why do you think this exists?
* Which component will handle this?
* What would happen if this changed?

Predictions provide a baseline for later verification.

---

## 3. Observation

Observe the real system.

Preferred observations include:

* terminal output
* packet captures
* logs
* process state
* memory layout
* filesystem state
* browser developer tools
* debugger output

Observation should come from the system itself whenever possible.

When practical work is needed, perform the experiment within this stage and
record the resulting observations before explaining them.

---

## 4. Explanation

Explain the observed behavior.

Connect:

```text
Observation

↓

Mechanism

↓

Mental Model
```

Avoid explaining only the command or syntax.

---

# Practical Experiments

Experiments should be:

* local
* reproducible
* observable
* reversible
* ethically scoped

Whenever possible, use:

* virtual machines
* containers
* loopback interfaces
* local web applications
* sample datasets
* intentionally vulnerable training environments

Avoid modifying the primary host unnecessarily.

---

# Application Rules

During a session:

* ask for a prediction before observation whenever practical
* record observed facts before interpretation
* distinguish conclusions from assumptions and speculation
* connect the observed mechanism to related system components
* investigate unexpected results before revising the mental model
* prefer explaining behavior over memorizing commands

These rules apply the evidence, systems-thinking, and failure principles
defined in `DESIGN_PRINCIPLES.md` without restating them.

---

# Roadmap-Specific Session Modes

Some roadmaps may use session flows that extend the default Labs session cycle.

Roadmap-specific modes should be documented here when they affect how sessions are conducted.

## Unix Task-Solving Mode

The `unix/` roadmap uses a learner-first task-solving mode.

Typical flow:

```text
Setup

↓

Task Prompt

↓

Learner Solves in Terminal

↓

Review and Correction

↓

notes.md Only When Requested
```

In this mode, ChatGPT should provide the session setup and task prompts, then wait for the learner's attempted commands or answers.

The learner performs the primary work directly in the terminal.

ChatGPT should then:

* review the learner's attempted solution
* identify mistakes or mismatches with the task
* explain only the necessary corrections
* reinforce the reusable command pattern
* generate `notes.md` only when explicitly requested

This mode differs from normal concept-led sessions because the primary learning action is the learner's direct problem solving.

The assistant should avoid immediately solving the task unless the learner asks for help or is blocked.

Temporary handoff files may be used to transfer session context to an LLM, but they are not project rules and should not be treated as persistent templates.

### Planned Workload

Ordinary Unix sessions should be designed for approximately 20 minutes of
planned work. This is a workload-design target, not a hard runtime cutoff.
Unexpected troubleshooting may take longer, but troubleshooting must not
silently expand the planned curriculum or create additional core Tasks.

### Core Task Budget

Ordinary Unix sessions have at most five core Tasks. Review sessions use the
same limit.

Setup, verification, Wrap-up, and explicitly requested notes generation are
not core Tasks. Do not automatically introduce Task 6. Correction attempts
remain part of the current Task rather than becoming new curriculum Tasks.

If one Task overruns, reduce, defer, or reschedule later planned Tasks. Do not
silently append more work because a phase has no clear endpoint.

### Unexpected New Concepts

When a correction would require a substantial new syntax or conceptual topic:

* explain the immediate mismatch only as needed
* do not turn the current session into an unplanned language lesson
* defer or reschedule the concept, provide a bounded helper, or revise the
  roadmap explicitly
* require explicit approval for substantive curriculum expansion

### Learner-Authored Complexity Boundary

Routine learner-authored Unix Tasks may require:

* fixed-field `awk` extraction
* `-F`
* simple comparisons
* simple regular expressions
* a clear one-step `sub()` or `substr()` transformation
* short pipelines where each tool has a distinct role

Routine learner-authored Unix Tasks should not require:

* associative arrays
* `count[key]++`
* stateful aggregation
* complex `END` blocks
* nested parser control flow
* dynamic general-purpose `key=value` parsing
* maximum or minimum state plus owner-record preservation
* large shell reporting blocks
* deeply nested command substitutions

Advanced syntax is not forbidden. It may appear as an optional explanation, a
provided bounded helper, or part of an explicitly approved advanced session.
It must not become a hidden prerequisite for progressing through the
foundational Unix roadmap.

### Composability Preference

When equivalent for the learning goal, prefer:

```text
small filter or extract command
→ sort
→ uniq -c
→ optional ranking
```

over a monolithic `awk` program that performs filtering, parsing, aggregation,
and reporting internally. This is a learning-design preference, not an
absolute performance claim.

### Integrated Reporting Boundary

Do not use multi-metric, multi-variable `printf` reporting blocks as ordinary
integrated Unix assignments. `printf` itself is not prohibited, and a simple
`printf` example may be used when directly relevant. The excluded pattern is a
large synthesized report whose shell syntax becomes the primary difficulty.

### Completion and Notes

A Unix task-solving session still requires a direct learner attempt, review or
correction, and an evidence-based explanation. Completion depends on
demonstrated understanding rather than raw Task count. Generate `notes.md` only
when explicitly requested.

---

# Session Completion

A session is considered complete when the learner can:

* explain the concept in their own words
* connect it to related systems
* interpret observed behavior
* justify conclusions with evidence
* predict similar behavior in related situations

For Unix task-solving sessions, completion also requires that the learner has attempted the task directly and received review or correction.

Completion is based on demonstrated understanding rather than the number of commands or facts covered.

---

# Markdown Generation

When a session produces or modifies Markdown covered by the repository Markdown policy, including files under `active/**`, `backlog/**`, `archive/**`, or `ops/docs/**`, follow:

```text
ops/docs/MARKDOWN_GENERATION_POLICY.md
```

Before committing Markdown changes, validate with the repository markdownlint command documented in that policy.

---

# Scope

These rules apply to every roadmap within the Labs project unless a roadmap explicitly defines additional procedures.

Roadmap-specific additions may extend these rules but should not silently
contradict `DESIGN_PRINCIPLES.md`.
