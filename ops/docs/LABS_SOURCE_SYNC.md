# Labs Source and Git Policy

## Purpose

This document defines how local files, Git branches, GitHub, observed system
output, AI context, and handoffs establish state for Labs Curriculum V2.

## Source-of-Truth Precedence

Use this order when sources conflict:

```text
current committed repository file
>
current observed system output
>
focused branch or diff evidence
>
explicit handoff
>
accumulated chat context
```

Project Sources, cached uploads, AI chats, summaries, outputs, hypotheses,
generated reports, and AI memory are not canonical. They may provide working
context but must not override current committed files or direct observed
evidence.

For runtime questions, current observed output is the evidence for what the
system did. It does not silently change repository policy, roadmap sequence,
or committed progress.

## Canonical Git State

After Curriculum V2 is merged:

```text
main = current canonical curriculum and committed progress
```

The local working tree is where changes are inspected before commit. The
GitHub remote provides committed state to tools that cannot see local files.
Uncommitted changes are not visible through the remote; provide a focused diff
or exact file content when another tool must review them.

Always identify the repository root, current branch, current commit, and
working-tree state before broad changes or a handoff.

## Focused Branch and Diff Evidence

A branch or diff is evidence only for the paths and commit range inspected.
Check the branch's base before assuming that it represents current `main`.
When uncommitted changes matter, inspect both staged and unstaged diffs.

Do not silently rebase, merge, switch branches, reset, discard changes, rewrite
history, or force push. Report a baseline conflict before modifying files.

## Short-Lived Branch Policy

Permitted branch patterns are:

```text
learn/<roadmap>_pXX_sYY
fix/<bounded_issue>
refactor/<bounded_refactor>
```

Branches should have one bounded purpose and merge or close after review. Do
not create long-lived branches named for roadmap directories.

Obsolete topic branches may remain after the V2 reset. Treat them as cleanup
candidates, not curriculum sources:

1. List whether they exist locally or remotely.
2. Do not merge their earlier session content into V2.
3. Do not delete them without explicit branch-cleanup authority.
4. If a path-specific diff shows unique non-curriculum governance or tooling,
   report the exact paths for separate review.

## Committed Progress

Progress is determined from the current roadmap specification and committed
session files whose front matter contains `status: completed`. Chat claims,
placeholder files, directory existence, Project Sources, and uncommitted
templates are not completed-session evidence.

Curriculum V2 uses no separate progress database or progress checkboxes.

## Observed System Output

Record only output actually produced by the learner's authorized environment.
Preserve the command or action, relevant environment, and minimum evidence
needed to support the conclusion. An assistant-generated example is not an
observation.

Before committing an observation, redact it according to
[Markdown Generation Policy](MARKDOWN_GENERATION_POLICY.md). Private-program
or unsafe target evidence belongs in a separate private workspace.

## AI Context and Generated Artifacts

AI chats, provider history, memory, summaries, hypotheses, generated code, and
draft reports are assistance artifacts, not project state or direct evidence.
They become part of canonical repository state only after a human verifies the
material claims, applies publication and data-handling rules, and commits the
resulting repository file.

AI-generated text must never replace the observed command, packet, frame,
request, response, source definition, or runtime state needed to support a
claim. Follow [AI-Assisted Learning and
Research](AI_ASSISTED_LEARNING_AND_RESEARCH.md) for verification, privacy, and
untrusted-content rules.

## Project Sources and Cached Context

Stable governance files may be kept as Project Sources for convenience, but
the current committed repository remains authoritative. Useful references are:

- `LABS_SOURCE_SYNC.md`
- `DESIGN_PRINCIPLES.md`
- `LABS_SESSION_RULES.md`
- `AI_ASSISTED_LEARNING_AND_RESEARCH.md`
- `MARKDOWN_GENERATION_POLICY.md`
- `ROADMAP_FORMAT.md`
- `ROADMAP_INDEX.md`
- `SESSION_ARCHIVE_FORMAT.md`

Load the relevant roadmap README from its current committed lifecycle path.
Do not keep ordinary session archives, private evidence, handoffs, or changing
progress summaries as authoritative cached sources.

## Conversation Context

The current conversation may supply a bounded instruction or clarification,
but it is not proof of repository state, runtime output, or completed progress.
Inspect the relevant files and evidence before continuing a roadmap.

Organize a conversation around one meaningful roadmap section or coherent
workstream. Split materially divergent troubleshooting or maintenance rather
than letting accumulated context redefine the curriculum.

## Handoffs

Use a handoff only when cross-session transfer is useful. It should contain:

- what was verified
- what remains unresolved
- exact paths, commands, or evidence
- current branch and commit
- next bounded action

Verify handoff claims against the repository and current observations. When
they conflict, the higher source in the precedence order wins.

## Conflict Handling

When two sources disagree:

1. Identify both sources and their dates, branches, commits, or paths.
2. Apply the source-of-truth precedence.
3. Verify the current committed file and relevant observed evidence.
4. Report unresolved ambiguity instead of silently combining rules.
5. Update stale cached context only after the canonical change is reviewed and
   committed.

## Session Bootstrap

Before continuing learning work:

1. Read this policy and the [Roadmap Index](ROADMAP_INDEX.md).
2. Identify the current branch and commit.
3. Load the relevant roadmap README from its current lifecycle directory.
4. Determine progress only from committed completed-session files.
5. Confirm the authorized environment and next documented session.
6. Follow [Labs Session Rules](LABS_SESSION_RULES.md).
