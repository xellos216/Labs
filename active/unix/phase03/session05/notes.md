# Session05 Notes

## Core Idea

Session05 focused on journal-style multi-service logs.

The main goal was to observe how one service failure can appear across several
processes and how process restarts change runtime identity.

The log contained:

```text
systemd
postgres
api
nginx
```

The stable prefix was:

```text
$1 = timestamp
$2 = process[PID]
$3 = level
$4 onward = message
```

The main investigation flow was:

```text
normalize process identity
→ aggregate severity
→ observe PID changes
→ extract failure timeline
→ follow failure propagation
→ verify recovery
```

---

## Task1

Normalize `process[PID]` and count logs by process.

```bash
awk '{
  process_name = $2
  sub(/\[[0-9]+\]$/, "", process_name)
  print process_name
}' journal.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
9 systemd
9 api
7 postgres
7 nginx
```

Pattern:

```text
process[PID]
→ remove PID suffix
→ process name
→ aggregate
```

The regular expression:

```awk
/\[[0-9]+\]$/
```

means:

```text
\[
→ literal [

[0-9]+
→ one or more digits

\]
→ literal ]

$
→ end of string
```

---

## Task2

Count events by log level.

```bash
awk '{print $3}' journal.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
22 INFO
6 ERROR
4 WARN
```

Pattern:

```text
extract level
→ group
→ count
→ rank
```

---

## Task3

Count `ERROR` events by process.

```bash
awk '$3 == "ERROR" {
  process_name = $2
  sub(/\[[0-9]+\]$/, "", process_name)
  print process_name
}' journal.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
2 postgres
2 nginx
2 api
```

The important distinction was:

```text
filter first
→ normalize process identity
→ aggregate
```

---

## Task4

Observe PostgreSQL PID changes.

A direct inspection command was:

```bash
awk '$2 ~ /^postgres\[/' journal.log
```

The important process identities were:

```text
postgres[510]
postgres[845]
```

A compact unique-process form is:

```bash
awk '$2 ~ /^postgres\[/ && !seen[$2]++ {
  print $2
}' journal.log
```

Observed relationship:

```text
postgres[510]
→ failure and shutdown

postgres[845]
→ restarted PostgreSQL process
```

The PID change showed that PostgreSQL was not merely changing state inside
the same process.

A new process instance was created.

---

## Task5

Extract the PostgreSQL failure and restart timeline.

```bash
awk \
  -v start='2026-07-20T08:03:11+09:00' \
  -v end='2026-07-20T08:04:20+09:00' \
  '$1 >= start && $1 <= end &&
   ($2 ~ /^postgres\[/ || $2 == "systemd[1]") {
     print
   }' \
  journal.log
```

Observed timeline:

```text
08:03:11 postgres[510] WARN  checkpoint delayed
08:04:01 postgres[510] ERROR NO_SPACE_LEFT
08:04:06 postgres[510] ERROR shutting down
08:04:06 systemd[1]   WARN  postgres.service entered failed state
08:04:15 systemd[1]   INFO  Starting PostgreSQL
08:04:17 postgres[845] INFO  starting up
08:04:20 postgres[845] INFO  ready to accept connections
08:04:20 systemd[1]   INFO  Started PostgreSQL
```

Pattern:

```text
warning
→ storage error
→ process shutdown
→ systemd observes failure
→ service restart
→ new PID
→ ready state
```

---

## Task6

Observe how the PostgreSQL failure appeared in upstream services.

```bash
awk \
  -v start='2026-07-20T08:04:01+09:00' \
  -v end='2026-07-20T08:04:24+09:00' \
  '$1 >= start && $1 <= end &&
   ($2 ~ /^postgres\[/ || $2 ~ /^api\[/ || $2 ~ /^nginx\[/) {
     print
   }' \
  journal.log
```

Observed timeline:

```text
08:04:01 postgres ERROR NO_SPACE_LEFT
08:04:03 api      ERROR database_write_failed status=500
08:04:04 nginx    ERROR upstream_response status=500
08:04:06 postgres ERROR shutting down
08:04:09 api      ERROR CONNECTION_REFUSED status=503
08:04:10 nginx    ERROR upstream_response status=503
08:04:17 postgres INFO  starting up
08:04:20 postgres INFO  ready
08:04:24 api      INFO  database_connection_recovered
```

Observed propagation:

```text
postgres storage failure
→ API write failure
→ nginx 500
→ PostgreSQL shutdown
→ API connection refused
→ nginx 503
→ PostgreSQL restart
→ API DB connection recovery
```

The timing and error messages strongly correlate the events.

They do not by themselves prove that PostgreSQL was the only possible cause.

---

## Task7

Aggregate HTTP status codes from `api` and `nginx` during the failure window.

A simple composable approach is:

```bash
awk \
  -v start='2026-07-20T08:04:01+09:00' \
  -v end='2026-07-20T08:04:24+09:00' \
  '$1 >= start && $1 <= end &&
   ($2 ~ /^api\[/ || $2 ~ /^nginx\[/) {
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^status=/)
        print substr($i, 8)
    }
  }' \
  journal.log |
sort |
uniq -c |
sort -nr
```

The important design choice was to keep the responsibilities separate:

```text
awk
→ filter and extract

sort
→ group identical values

uniq -c
→ count

sort -nr
→ rank
```

A more complex associative-array implementation was possible but was not
necessary for this task.

---

## Task8

Aggregate status codes by normalized process name.

```bash
awk \
  -v start='2026-07-20T08:04:01+09:00' \
  -v end='2026-07-20T08:04:24+09:00' \
  '$1 >= start && $1 <= end &&
   ($2 ~ /^api\[/ || $2 ~ /^nginx\[/) {
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^status=/) {
        process_name = $2
        sub(/\[[0-9]+\]$/, "", process_name)
        print process_name, substr($i, 8)
      }
    }
  }' \
  journal.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
1 nginx 503
1 nginx 500
1 api 503
1 api 500
```

Observed result:

```text
api
→ 500
→ 503

nginx
→ 500
→ 503
```

Both the API and nginx reflected the failure in their HTTP status output.

---

## Task9

Verify recovery after PostgreSQL restarted.

```bash
awk \
  -v start='2026-07-20T08:04:20+09:00' \
  -v end='2026-07-20T08:04:32+09:00' \
  '$1 >= start && $1 <= end &&
   ($2 ~ /^postgres\[/ || $2 ~ /^api\[/ || $2 ~ /^nginx\[/) {
     print
   }' \
  journal.log
```

Observed recovery sequence:

```text
08:04:20 postgres[845] ready to accept connections
08:04:24 api[620]      database_connection_recovered
08:04:31 nginx[702]    /health status=200
08:04:32 api[620]      /health status=200
```

Verified relationship:

```text
PostgreSQL ready
→ API database connection recovered
→ nginx health request 200
→ API health request 200
```

This provided evidence that the service stack recovered after the PostgreSQL
restart.

---

## Notes

### Process Name Versus Process Instance

These are different concepts:

```text
postgres
→ logical process/service name

postgres[510]
→ one runtime process instance

postgres[845]
→ another runtime process instance
```

Normalizing away the PID is useful for aggregation.

Keeping the PID is useful for lifecycle analysis.

Mental model:

```text
aggregate by service
→ remove PID

observe restart
→ preserve PID
```

---

### PID Change as Runtime Evidence

Observed:

```text
postgres[510]
→ shutdown

postgres[845]
→ startup
```

This supports:

```text
old PostgreSQL process ended
→ new PostgreSQL process started
```

A service name can remain the same while the underlying process identity
changes.

---

### Multi-service Failure Propagation

A failure may appear differently at each layer.

In this session:

```text
PostgreSQL
→ NO_SPACE_LEFT

API
→ database_write_failed
→ CONNECTION_REFUSED

nginx
→ HTTP 500
→ HTTP 503
```

The same underlying incident was represented using different vocabulary at
different layers.

This is why cross-service timeline analysis is useful.

---

### Logical Service Layers

The observed dependency chain was approximately:

```text
client
↓
nginx
↓
API
↓
PostgreSQL
```

A lower-layer database failure appeared upstream as application and HTTP
errors.

The logs therefore exposed the dependency direction indirectly.

---

### Process Normalization

To remove `[PID]`:

```awk
process_name = $2
sub(/\[[0-9]+\]$/, "", process_name)
```

Example:

```text
nginx[702]
→ nginx

api[620]
→ api
```

Do not normalize the PID away when the investigation specifically concerns
process replacement or restart.

---

### One `if` Versus an `if` Block

Without braces:

```awk
if (condition)
  statement1
statement2
statement3
```

Only `statement1` is conditional.

With braces:

```awk
if (condition) {
  statement1
  statement2
  statement3
}
```

All three statements are conditional.

This mattered when extracting status values and normalizing process names.

Incorrect:

```awk
if ($i ~ /^status=/)
  process_name = $2

sub(...)
print ...
```

Correct:

```awk
if ($i ~ /^status=/) {
  process_name = $2
  sub(...)
  print ...
}
```

---

### Keep awk Small When Possible

A complex `awk` associative-array program could perform filtering,
aggregation, and sorting logic internally.

For this roadmap, a simpler Unix pipeline was generally preferable:

```text
awk
→ sort
→ uniq -c
→ sort
```

This makes each transformation explicit.

Example:

```bash
awk 'extract value' file |
sort |
uniq -c |
sort -nr
```

The goal is composable Unix reasoning, not maximizing the amount of logic
inside one `awk` program.

---

### Time-window Filtering

The journal used fixed-width ISO 8601 timestamps with the same timezone
offset.

Therefore:

```awk
$1 >= start && $1 <= end
```

could be used directly for chronological filtering.

Example:

```bash
awk \
  -v start='2026-07-20T08:04:01+09:00' \
  -v end='2026-07-20T08:04:24+09:00' \
  '$1 >= start && $1 <= end' \
  journal.log
```

---

### Recovery Requires Positive Evidence

An error disappearing from the log does not by itself prove recovery.

This session had explicit recovery evidence:

```text
PostgreSQL ready to accept connections
API database_connection_recovered
nginx /health status=200
API /health status=200
```

Mental model:

```text
failure evidence
→ restart evidence
→ dependency recovery evidence
→ successful health checks
```

---

## Common Mistakes

### Mistake 1

Incorrect PID-removal regular expression.

The required pattern was:

```awk
/\[[0-9]+\]$/
```

If the closing bracket is not matched correctly, values remain:

```text
postgres[510]
nginx[702]
api[620]
```

instead of:

```text
postgres
nginx
api
```

---

### Mistake 2

Misspelling the process name in a regular expression.

Incorrect:

```awk
$2 ~ /^postgre\[/
```

Correct:

```awk
$2 ~ /^postgres\[/
```

The incorrect expression silently excluded every PostgreSQL line.

---

### Mistake 3

Running several statements outside an `if` block.

Incorrect:

```awk
if ($i ~ /^status=/)
  process_name = $2

sub(...)
print ...
```

The normalization and printing occur for every field.

Correct:

```awk
if ($i ~ /^status=/) {
  process_name = $2
  sub(...)
  print ...
}
```

---

### Mistake 4

Using unnecessary complexity for aggregation.

Complex shape:

```text
awk associative array
→ END loop
→ internal aggregation
```

Simpler shape:

```text
awk extraction
→ sort
→ uniq -c
→ sort -nr
```

The simpler form better preserved the composable Unix model for this task.

---

## Reusable Patterns

Normalize `process[PID]`:

```bash
awk '{
  process_name = $2
  sub(/\[[0-9]+\]$/, "", process_name)
  print process_name
}' file
```

Filter one level and aggregate process names:

```bash
awk '$3 == "ERROR" {
  process_name = $2
  sub(/\[[0-9]+\]$/, "", process_name)
  print process_name
}' file |
sort |
uniq -c |
sort -nr
```

Observe unique process instances:

```bash
awk '$2 ~ /^process\[/ && !seen[$2]++ {
  print $2
}' file
```

Filter several process types in a time window:

```bash
awk \
  -v start='START' \
  -v end='END' \
  '$1 >= start && $1 <= end &&
   ($2 ~ /^process1\[/ ||
    $2 ~ /^process2\[/ ||
    $2 ~ /^process3\[/) {
     print
   }' \
  file
```

Extract a dynamic `status=` field:

```awk
for (i = 1; i <= NF; i++) {
  if ($i ~ /^status=/)
    print substr($i, 8)
}
```

Aggregate normalized process and status together:

```bash
awk 'condition {
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^status=/) {
      process_name = $2
      sub(/\[[0-9]+\]$/, "", process_name)
      print process_name, substr($i, 8)
    }
  }
}' file |
sort |
uniq -c |
sort -nr
```

---

## Final Mental Model

A journal-style log can expose both service-level and process-level state.

```text
service
↓
process instance
↓
PID
↓
runtime events
```

During normal operation:

```text
postgres
→ api
→ nginx
→ successful requests
```

During the observed incident:

```text
PostgreSQL checkpoint delay
→ NO_SPACE_LEFT
→ API write failure
→ nginx 500
→ PostgreSQL shutdown
→ API connection refused
→ nginx 503
```

During recovery:

```text
systemd restart
→ new PostgreSQL PID
→ PostgreSQL ready
→ API DB connection recovered
→ nginx health 200
→ API health 200
```

The central lesson was:

```text
aggregate by logical process name
when asking "which service?"

preserve PID
when asking "which process instance?"
```

Multi-service observability requires following the same incident through
different representations:

```text
database error
→ application error
→ HTTP error
→ restart
→ recovery evidence
```

The Unix pipeline provides a way to reduce the raw journal into exactly the
evidence needed for that question.
