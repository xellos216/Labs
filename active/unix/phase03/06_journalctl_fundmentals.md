# Session06 Notes

## Core Idea

Session06 focused on the fundamentals of `journalctl`.

The main goal was to understand the system journal as a structured collection
of log entries and to select relevant entries using simple filters.

The main investigation flow was:

```text
journal
↓
inspect recent entries
↓
inspect structured metadata
↓
filter by unit, priority, time, or tag
↓
combine filters for a bounded question
```

`journalctl` is best understood as a command-line interface for querying and
filtering entries stored in the systemd journal.

```text
systemd-journald
→ collects journal entries

journal
→ stores structured log entries

journalctl
→ queries and filters those entries
```

---

## Task1

Inspect the most recent journal entries.

```bash
journalctl -n 10 --no-pager
```

The important options were:

```text
-n 10
→ show the most recent 10 entries

--no-pager
→ print directly instead of opening a pager
```

Without `--no-pager`, `journalctl` may display output through a pager such as
`less`.

A default journal entry was observed in approximately this form:

```text
timestamp hostname identifier[PID]: message
```

Example structure:

```text
Aug 12 04:34:42 host systemd[956]: message
```

The important distinction was:

```text
systemd
→ message source / identifier

956
→ PID of that process
```

Therefore:

```text
systemd[956]
```

does not simply mean "process-level log."

It identifies a message associated with a `systemd` process whose PID was
`956`.

Kernel entries appeared with an identifier such as:

```text
kernel:
```

indicating that the message came from the kernel.

---

## Task2

Inspect the structured fields behind the default journal output.

```bash
journalctl -n 1 -o verbose
```

The prediction that verbose output might expose a memory location was
incorrect.

Instead, `-o verbose` exposed structured journal metadata.

Observed fields included examples such as:

```text
_BOOT_ID=
_MACHINE_ID=
_HOSTNAME=
_RUNTIME_SCOPE=
_TRANSPORT=
PRIORITY=
SYSLOG_IDENTIFIER=
MESSAGE=
```

For a kernel entry, the relevant observed fields included:

```text
_TRANSPORT=kernel
SYSLOG_IDENTIFIER=kernel
PRIORITY=4
MESSAGE=...
```

This showed that the compact default output is only one presentation of a
journal entry.

Mental model:

```text
structured journal entry
↓
many KEY=VALUE fields
↓
journalctl default output
↓
selected fields rendered as one readable line
```

A later process entry exposed fields including:

```text
_COMM=tailscaled
_PID=723
SYSLOG_IDENTIFIER=tailscaled
_EXE=/usr/bin/tailscaled
_SYSTEMD_UNIT=tailscaled.service
_TRANSPORT=stdout
MESSAGE=...
```

This demonstrated that one log entry may contain information about:

```text
process name
PID
executable
systemd unit
transport
message
```

The observed relationship was:

```text
tailscaled[723]: message
        ↓
SYSLOG_IDENTIFIER=tailscaled
_PID=723
MESSAGE=...
```

---

## Task3

Select journal entries by systemd unit.

```bash
journalctl -u tailscaled.service -n 5 --no-pager
```

The relevant verbose field previously observed was:

```text
_SYSTEMD_UNIT=tailscaled.service
```

The result contained entries associated with that service.

Mental model:

```text
journal
↓
-u tailscaled.service
↓
entries associated with tailscaled.service
```

This established `-u` as the normal filter when the question is about one
specific systemd unit.

---

## Task4

Filter journal entries using priority, time, and tag.

### Priority

```bash
journalctl -p err -n 10 --no-pager
```

A selected entry was inspected with:

```bash
journalctl -p err -n 1 -o verbose --no-pager
```

The observed value was:

```text
PRIORITY=3
```

The standard priority ordering is:

```text
0 emerg
1 alert
2 crit
3 err
4 warning
5 notice
6 info
7 debug
```

Lower numeric values represent higher severity.

Therefore:

```text
-p err
```

selects `err` and more severe priorities:

```text
0..3
```

Mental model:

```text
-p err
↓
emerg / alert / crit / err
```

### Time

```bash
journalctl --since "10 minutes ago" --no-pager
```

The important correction was that `--since` does not imply an error filter.

It restricts the journal by time only.

The observed output contained several different sources and message types,
including entries from:

```text
kernel
sshd-session
systemd-logind
tailscaled
```

Therefore:

```text
--since
→ time filter

-p
→ priority filter
```

They answer different questions.

### Tag

```bash
journalctl -t tailscaled -n 5 --no-pager
```

The relevant journal field is:

```text
SYSLOG_IDENTIFIER=tailscaled
```

Mental model:

```text
-t tailscaled
↓
SYSLOG_IDENTIFIER=tailscaled
```

This differs from:

```text
-u tailscaled.service
↓
systemd unit selection
```

During this session, the outputs of:

```bash
journalctl -u tailscaled.service -n 5 --no-pager
```

and:

```bash
journalctl -t tailscaled -n 5 --no-pager
```

were observed to be the same.

That observation does not mean `-u` and `-t` are equivalent.

They select entries using different identities:

```text
-u
→ unit identity

-t
→ syslog identifier / tag
```

The selected sets happened to overlap for the observed `tailscaled` entries.

---

## Task5

Combine several journal filters to answer one bounded question.

Target question:

```text
Show at most 10 err-or-higher entries from tailscaled.service
during the last 30 minutes.
```

The first attempt was:

```bash
journalctl -t tailscaled -p err -n 10 --no-pager
```

This correctly included:

```text
priority filter
count limit
pager behavior
```

but missed the time constraint and used the tag identity rather than the
requested systemd unit identity.

The corrected command was:

```bash
journalctl \
  -u tailscaled.service \
  --since "30 minutes ago" \
  -p err \
  -n 10 \
  --no-pager
```

Each option has one responsibility:

```text
-u tailscaled.service
→ unit

--since "30 minutes ago"
→ time window

-p err
→ priority

-n 10
→ result limit

--no-pager
→ output presentation
```

Reusable pattern:

```text
journalctl
+ source selector
+ time selector
+ severity selector
+ output limit
```

---

## Notes

### What `journalctl` Means

The command name can be read as:

```text
journal + ctl
```

`ctl` is commonly associated with "control" in system-oriented command names.

For ordinary use, however, the important role of `journalctl` is querying and
inspecting the systemd journal.

Mental model:

```text
journalctl
≠ the journal itself

journalctl
→ interface used to query the journal
```

---

### Journal Entries Are Structured Data

The normal output may look like a traditional text log:

```text
timestamp host source[PID]: message
```

but the verbose output demonstrated that a journal entry contains structured
metadata.

```text
default output
→ readable representation

-o verbose
→ underlying journal fields
```

This is why `journalctl` can filter entries using properties such as:

```text
unit
priority
identifier
time
```

without manually parsing every displayed line.

---

### `--since` and Natural-Language-Like Time Syntax

This command is valid:

```bash
journalctl --since "30 minutes ago"
```

`"30 minutes ago"` looks like natural language, but the shell does not
understand its time meaning.

The shell's responsibility is to preserve it as one argument.

Conceptually:

```text
argv[0] = journalctl
argv[1] = --since
argv[2] = 30 minutes ago
```

The quotation marks prevent the spaces from splitting the value into several
shell arguments.

Then `journalctl` / systemd's time parser interprets the string according to
its supported time syntax.

Mental model:

```text
shell
↓
preserve "30 minutes ago" as one argument
↓
journalctl receives the string
↓
systemd time parser interprets it
↓
timestamp boundary
↓
journal entries selected
```

This is not arbitrary natural-language understanding.

It is a defined parser accepting natural-language-like time expressions.

The same shell/program boundary appears elsewhere:

```bash
grep "hello world" file
```

Here:

```text
shell
→ passes "hello world" as one argument

grep
→ interprets that argument as a pattern
```

The shell transports arguments.

The receiving program defines their meaning.

---

### `-u` Versus `-t`

These filters can produce identical output while representing different
questions.

```text
-u SERVICE
→ which systemd unit?

-t TAG
→ which SYSLOG_IDENTIFIER?
```

Example:

```bash
journalctl -u tailscaled.service
journalctl -t tailscaled
```

During this session their observed outputs matched.

The correct conclusion is:

```text
different selectors
↓
same observed result set in this case
```

not:

```text
-u == -t
```

---

### Observation Versus Interpretation

A journal query directly supports conclusions about the entries it selected.

For example:

```text
journalctl --since "10 minutes ago"
```

can show:

```text
these entries were recorded in the selected time window
```

It does not by itself establish:

```text
why those events occurred
```

Likewise, an empty result from:

```bash
journalctl \
  -u tailscaled.service \
  --since "30 minutes ago" \
  -p err \
  --no-pager
```

would support:

```text
no matching journal entries were returned
```

rather than a broader claim that the service had no problem of any kind.

---

## Common Mistakes

### Mistake 1

Treating:

```text
systemd[956]
```

as merely meaning "process-level log."

More precise interpretation:

```text
systemd
→ identifier/source

956
→ PID
```

---

### Mistake 2

Predicting that:

```bash
journalctl -o verbose
```

would expose a memory location.

Observed behavior:

```text
-o verbose
→ structured journal metadata
```

---

### Mistake 3

Assuming:

```bash
journalctl --since "10 minutes ago"
```

means:

```text
errors during the last 10 minutes
```

Correct model:

```text
--since
→ time only

-p err
→ severity
```

To combine them:

```bash
journalctl --since "10 minutes ago" -p err --no-pager
```

---

### Mistake 4

Using a tag filter when the task specifically asks for a systemd unit.

Tag:

```bash
journalctl -t tailscaled
```

Unit:

```bash
journalctl -u tailscaled.service
```

Choose the selector based on the identity the question actually asks about.

---

### Mistake 5

Forgetting one dimension of a multi-filter question.

A useful construction method is:

```text
What source?
→ -u / -t

What time?
→ --since / --until

What severity?
→ -p

How many?
→ -n

How should output be displayed?
→ --no-pager / -o ...
```

Build the command from the question rather than trying to recall one complete
command from memory.

---

## Reusable Patterns

Show recent entries:

```bash
journalctl -n 10 --no-pager
```

Inspect structured metadata:

```bash
journalctl -n 1 -o verbose
```

Filter one systemd unit:

```bash
journalctl -u SERVICE --no-pager
```

Filter by identifier/tag:

```bash
journalctl -t IDENTIFIER --no-pager
```

Filter by priority:

```bash
journalctl -p err --no-pager
```

Filter by relative time:

```bash
journalctl --since "10 minutes ago" --no-pager
```

Combine unit, time, priority, and count:

```bash
journalctl \
  -u SERVICE \
  --since "30 minutes ago" \
  -p err \
  -n 10 \
  --no-pager
```

Inspect the metadata of one filtered result:

```bash
journalctl \
  -u SERVICE \
  -p err \
  -n 1 \
  -o verbose \
  --no-pager
```

---

## Final Mental Model

The journal is not merely a text file containing lines.

It can be viewed as a collection of structured entries:

```text
journal
↓
entry
├── timestamp
├── process identity
├── PID
├── systemd unit
├── priority
├── transport
├── identifier
└── message
```

`journalctl` asks questions about those entries.

```text
all journal entries
↓
select a source
↓
select a time range
↓
select a priority
↓
limit or format the result
↓
observe evidence
```

The central lesson was:

```text
journalctl
→ select relevant evidence from the system journal
```

rather than:

```text
journalctl
→ dump a log file
```

Different options represent different selection dimensions:

```text
-u
→ unit

-t
→ identifier/tag

-p
→ priority

--since / --until
→ time

-n
→ number of returned entries

-o
→ output representation
```

The reusable Unix reasoning pattern is:

```text
question
↓
identify the required dimensions
↓
select the smallest relevant journal subset
↓
inspect the resulting evidence
↓
separate observation from causal interpretation
```

## Next Session

```text
Phase 03
Session 07
Live Logs with logger and journalctl -f
```
