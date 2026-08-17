# Markdown Generation Policy

## Purpose and Scope

This policy governs Markdown created or modified in Labs Curriculum V2:

```text
README.md
AGENTS.md
LICENSE.md
THIRD_PARTY_NOTICES.md
active/**/*.md
backlog/**/*.md
archive/**/*.md
ops/**/*.md
```

Verified canonical legal text under `LICENSES/` is outside normal Markdown
generation, formatting normalization, and linting.

## Source and Language

Current repository files and `.markdownlint-cli2.jsonc` define the formatting
baseline. Cached Project Sources and assistant memory must not override them.

Use English for governance, roadmap specifications, repository navigation,
filenames, branch examples, and commit examples. Use Korean for completed
session archive headings and body, including learner observations and
explanations. Preserve commands, protocol names, APIs, errors, and technical
identifiers in their original form.

## Formatting Rules

- Put blank lines around lists, headings, block quotes, and fenced code.
- Add a language identifier to every fenced code block.
- Use one trailing newline at the end of each file.
- Remove trailing spaces and repeated blank lines.
- Use English snake_case for learner-created session and experiment filenames;
  preserve canonical governance, template, root, and legal filenames.
- Prefer concise headings and measurable verbs.
- Keep relative links repository-valid.
- Do not rewrite learner language or observed facts for style alone.

Roadmap README files are English curriculum specifications. Completed-session
files are Korean evidence records. Do not combine those roles in one document.

## Evidence and Interpretation

Never present an unobserved command result, packet, log, browser state,
response, or successful test as fact. Separate:

- observed evidence
- interpretation supported by that evidence
- inference requiring another test
- limitation or uncertainty

Include only the minimum raw output needed to support the explanation. Do not
copy a raw chat transcript into the repository. Preserve failed hypotheses
when they clarify the final mental model or false-positive rejection.

## AI-Derived Markdown

Apply [AI-Assisted Learning and
Research](AI_ASSISTED_LEARNING_AND_RESEARCH.md) to Markdown derived from AI
assistance.

- Manually verify every factual claim against the underlying source or direct
  evidence.
- Never invent commands, output, packets, frames, requests, responses, source
  behavior, or environment state.
- Do not paste sensitive prompts or full private AI transcripts.
- Redact secrets, account data, private-program details, and target identifiers
  before any AI use.
- Record material assistance in the completed-session archive.
- Do not treat AI citations or summaries as substitutes for the underlying
  source.
- Review drafts for text or instructions introduced through prompt injection
  in untrusted source material.
- Do not publish private-program or undisclosed material.

## Public Security-Research Boundary

Labs is public or publication-oriented. Do not commit:

- private bug bounty program data
- undisclosed vulnerability details
- scope-restricted target information
- live cookies, tokens, credentials, or secrets
- personal account data
- unredacted packet captures
- confidential third-party source
- prohibited exploit material
- real target identifiers when disclosure is unsafe

Real private-program evidence belongs in a separate private workspace. The
public repository may contain:

- local-lab results
- authorized training-platform results
- public disclosed-report analysis
- sanitized mechanism notes
- redacted examples
- generic and reusable research workflows

Repository publication eligibility does not expand authorization to perform a
test. Authorization and disclosure safety must both be satisfied.

## Identifier Redaction

Mask or replace identifiers that expose a real environment, person, account,
or target, including:

- WAN and revealing private IP addresses
- MAC addresses and stable host-specific interface names
- usernames, hostnames, account IDs, email addresses, and personal paths
- program names or asset identifiers covered by private scope
- session IDs, cookies, API keys, OAuth tokens, and authentication material
- request IDs or timestamps when they can correlate private activity

Use documentation ranges and generic identifiers when the relationship matters:

```text
IPv4: 192.0.2.10, 198.51.100.23, 203.0.113.8
IPv6: 2001:db8::10
MAC: aa:bb:cc:dd:ee:ff
Interfaces: eth0, wlan0
Accounts: account_a, account_b, admin_test
Targets: app.example.test, api.example.test
```

Review redaction manually. Pattern searches can miss encoded, truncated,
contextual, or image-based secrets.

## Images, Captures, and Other Media

Before adding public media, inspect:

- visible pixels or rendered content
- filename and surrounding Markdown context
- embedded metadata when present
- whether the source and license permit publication

Look for terminal prompts, usernames, hostnames, paths, tokens, QR codes,
notifications, window titles, tabs, target names, network identifiers, and
background content. Text grep does not replace manual image inspection.

Unredacted captures and private research artifacts must remain outside the
public repository. Prefer small sanitized excerpts in the archive over binary
evidence when the excerpt is sufficient.

List tracked media with a Git-aware command:

```bash
git ls-files -- '*.png' '*.jpg' '*.jpeg' '*.gif' '*.webp' '*.svg' '*.pdf' \
  '*.pcap' '*.pcapng' '*.har' '*.mp4' '*.webm'
```

Use an available metadata viewer such as `exiftool`; do not install a new tool
solely for this check. Request human review when visual or metadata inspection
cannot be completed.

## Session Archive Generation

Follow [Session Archive Format](SESSION_ARCHIVE_FORMAT.md). Create one archive
only after actual session completion and explicit learner approval. Use the
real completion date, `status: completed`, Korean required sections, and an
English snake_case filename.

Do not create incomplete session files, placeholder archives, separate notes
or QA files, or progress checkboxes. A template is not progress evidence.

## Canonical Legal Text

Preserve verified publisher-supplied legal text under `LICENSES/`
byte-for-byte. Do not reflow, translate, trim, normalize, or lint it. A warning
caused by canonical formatting must be documented rather than fixed in the
source text.

`LICENSE.md` and `THIRD_PARTY_NOTICES.md` are normal repository Markdown but
must not be changed for style or curriculum wording when their legal scope and
internal links remain valid.

## Git Metadata and History

Public commits expose author name and email. Review configured identity before
publishing:

```bash
git config --get user.name
git config --get user.email
git log --all --format='%h %an <%ae>' | sort -u
```

Deleting or redacting a current file does not remove older copies from commits,
tags, branches, forks, or clones. Report historical exposure for human review.
Do not rewrite history as part of ordinary documentation cleanup.

## Required Validation

Run Markdown lint across all normal Markdown, including `ops/templates/` and
`ops/scripts/`, while excluding `LICENSES/`:

```bash
npx markdownlint-cli2@latest \
  "README.md" \
  "AGENTS.md" \
  "LICENSE.md" \
  "THIRD_PARTY_NOTICES.md" \
  "active/**/*.md" \
  "backlog/**/*.md" \
  "archive/**/*.md" \
  "ops/**/*.md"
```

Expected result:

```text
Summary: 0 error(s)
```

Also inspect:

```bash
git diff --check
git diff --stat
git diff --name-status
```

For public documents derived from real output, search at minimum for MAC
addresses and unexpected IPv4 literals, then interpret every match:

```bash
rg -n --pcre2 '\b[0-9A-Fa-f]{2}(?::[0-9A-Fa-f]{2}){5}\b' \
  README.md AGENTS.md active backlog archive ops
rg -n --pcre2 '\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}\b' \
  README.md AGENTS.md active backlog archive ops
```

Validate local relative Markdown links with existing repository tooling or a
temporary uncommitted check. Do not add a dependency solely for link checking.

## Agent Requirements

An agent modifying repository Markdown must read this policy, keep changes in
scope, preserve legal text, avoid fabricating evidence, apply publication
boundaries, run the relevant validation, and report failed or unavailable
checks without claiming success.
