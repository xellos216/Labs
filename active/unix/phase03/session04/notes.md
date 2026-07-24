# Session04 Notes

## Core Idea

Session04 moved from isolated log aggregation to incident-oriented timeline
reconstruction.

The session combined events from multiple components:

```text
auth
edge
sudo
app
db
worker
```

The central workflow was:

```text
extract identifiers
→ correlate events across components
→ narrow the time range
→ reconstruct the event sequence
→ separate observation from causal claims
```

A single identifier was not always sufficient.

The investigation changed correlation keys as the event moved through the
system:

```text
client IP
→ authenticated user
→ sudo action
→ application service event
```

---

## Task1

Count events by event type.

```bash
awk '{print $4}' incident.log |
sort |
uniq -c |
sort -nr
```

Pattern:

```text
event field
→ group identical events
→ count
→ rank by frequency
```

The event name was stored in the stable prefix:

```text
$1 = timestamp
$2 = component
$3 = level
$4 = event
```

---

## Task2

Count `WARN` and `ERROR` events by component.

```bash
awk '$3 == "WARN" || $3 == "ERROR" {
  print $2
}' incident.log |
sort |
uniq -c |
sort -nr
```

The condition must contain a complete comparison on both sides of `||`.

Incorrect:

```awk
$3 == "WARN" || "ERROR"
```

The non-empty string `"ERROR"` evaluates as true, so every record is selected.

Correct:

```awk
$3 == "WARN" || $3 == "ERROR"
```

Alternative:

```awk
$3 ~ /^(WARN|ERROR)$/
```

---

## Task3

Count failed login events by client IP.

```bash
awk '$4 == "login_failed" {
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^client=/)
      print substr($i, 8)
  }
}' incident.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
4 192.0.2.44
3 203.0.113.55
```

The `client=` field was not in a fixed position.

Examples:

```text
user=admin client=192.0.2.44
client=192.0.2.44 user=root
reason=bad_password client=192.0.2.44
```

The parser therefore scanned every field.

```awk
$i ~ /^client=/
```

identified the client field.

`client=` contains seven characters, so:

```awk
substr($i, 8)
```

returned the IP address.

---

## Task4

Count failed login events by username.

```bash
awk '$4 == "login_failed" {
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^user=/)
      print substr($i, 6)
  }
}' incident.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
4 admin
2 root
1 deploy
```

Pattern:

```text
login_failed events
→ find user=
→ remove key prefix
→ aggregate users
```

`user=` contains five characters, so the value begins at character six.

---

## Task5

Find client IPs that appeared in both failed and successful login events.

```bash
comm -12 \
  <(
    awk '$4 == "login_failed" {
      for (i = 1; i <= NF; i++)
        if ($i ~ /^client=/)
          print substr($i, 8)
    }' incident.log |
    sort -u
  ) \
  <(
    awk '$4 == "login_success" {
      for (i = 1; i <= NF; i++)
        if ($i ~ /^client=/)
          print substr($i, 8)
    }' incident.log |
    sort -u
  )
```

Dataset result:

```text
192.0.2.44
```

`comm` requires sorted inputs.

```text
-1 → suppress values unique to the first input
-2 → suppress values unique to the second input
-3 → suppress common values
```

Therefore:

```bash
comm -12
```

prints only values common to both inputs.

Process substitution allowed the pipelines to be used as file-like inputs
without creating temporary files.

---

## Task6

Reconstruct the IP-based timeline for the shared client.

```bash
shared_client=$(
  comm -12 \
    <(
      awk '$4 == "login_failed" {
        for (i = 1; i <= NF; i++)
          if ($i ~ /^client=/)
            print substr($i, 8)
      }' incident.log |
      sort -u
    ) \
    <(
      awk '$4 == "login_success" {
        for (i = 1; i <= NF; i++)
          if ($i ~ /^client=/)
            print substr($i, 8)
      }' incident.log |
      sort -u
    )
)

grep "$shared_client" incident.log
```

Observed timeline:

```text
14:00:06 login_failed user=admin
14:00:19 login_failed user=root
14:01:03 login_failed user=admin
14:01:15 rate_limit
14:02:02 login_failed user=deploy
14:02:09 login_success user=deploy
```

Verified conclusion:

```text
192.0.2.44 produced four failed login events across multiple usernames,
triggered a rate-limit event, and later successfully authenticated as deploy.
```

This does not prove malicious intent or compromise.

It identifies an investigation candidate.

---

## Task7

Extract the successful username associated with the shared client.

```bash
successful_user=$(
  grep "$shared_client" incident.log |
  awk '$4 == "login_success" {
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^user=/)
        print substr($i, 6)
    }
  }'
)
```

Dataset result:

```text
deploy
```

Search for all events containing that user:

```bash
grep "user=$successful_user" incident.log
```

Observed events included:

```text
14:01:28 login_success user=deploy client=198.51.100.30 method=publickey
14:02:02 login_failed user=deploy client=192.0.2.44
14:02:09 login_success user=deploy client=192.0.2.44
14:02:16 sudo command user=deploy
14:02:23 restart_requested user=deploy
```

The earlier public-key login used a different client IP.

It should not automatically be treated as part of the same incident.

The stronger correlation was:

```text
client=192.0.2.44
→ login_success user=deploy
→ sudo command user=deploy
→ restart_requested user=deploy
```

This demonstrated identifier transition:

```text
network identity
→ account identity
→ privileged action
→ service action
```

---

## Task8

Extract the service-restart event window.

```bash
awk \
  -v start='2026-07-19T14:02:09+09:00' \
  -v end='2026-07-19T14:02:46+09:00' \
  '$1 >= start && $1 <= end' \
  incident.log
```

Observed timeline:

```text
14:02:09 login_success
14:02:16 sudo command
14:02:23 restart_requested
14:02:31 service_stopped
14:02:38 upstream_failure
14:02:46 service_started
```

The ISO 8601 timestamps used:

```text
same date format
same timezone offset
fixed-width fields
```

Therefore lexical string order matched chronological order.

Observed relationship:

```text
deploy login success
→ root-targeted service restart command
→ application restart request
→ service stopped
→ 503 upstream failure
→ service started
```

The `upstream_failure` occurred while the application service was between
the stopped and started events.

That is temporal correlation, not proof that the restart was the only cause.

---

## Task9

Count edge status codes before the restart window.

Range:

```text
start: 2026-07-19T14:00:00+09:00
end:   2026-07-19T14:02:31+09:00
```

```bash
awk \
  -v start='2026-07-19T14:00:00+09:00' \
  -v end='2026-07-19T14:02:31+09:00' \
  '$1 >= start && $1 < end && $2 == "edge" {
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^status=/)
        print substr($i, 8)
    }
  }' incident.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
2 200
1 429
```

The end boundary was exclusive:

```awk
$1 < end
```

This prevented the next time window from overlapping the first.

---

## Task10

Count edge status codes during and shortly after the restart.

Range:

```text
start: 2026-07-19T14:02:31+09:00
end:   2026-07-19T14:04:00+09:00
```

```bash
awk \
  -v start='2026-07-19T14:02:31+09:00' \
  -v end='2026-07-19T14:04:00+09:00' \
  '$1 >= start && $1 < end && $2 == "edge" {
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^status=/)
        print substr($i, 8)
    }
  }' incident.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
1 503
1 500
1 200
```

Comparison:

```text
Before restart:
2 200
1 429

During and shortly after restart:
1 503
1 500
1 200
```

Verified observation:

```text
The restart window contained both server-error responses and one successful
response.
```

Unsupported conclusion:

```text
The service was completely unavailable for the entire restart window.
```

The presence of `200` shows that complete unavailability was not observed.

---

## Task11

Reconstruct the DB timeout and recovery timeline.

```bash
awk \
  -v start='2026-07-19T14:03:18+09:00' \
  -v end='2026-07-19T14:04:02+09:00' \
  '$1 >= start && $1 <= end' \
  incident.log
```

Observed timeline:

```text
14:03:18 edge   request_failed      error=DB_TIMEOUT
14:03:24 db     connection_timeout  error=DB_TIMEOUT
14:03:41 worker job_failed          error=DB_TIMEOUT
14:04:02 db     connection_recovered
```

Observed relationship:

```text
DB_TIMEOUT appeared in edge, db, and worker events during the same interval.
The interval ended with a db connection_recovered event.
```

The final event required an inclusive end boundary:

```awk
$1 <= end
```

Using `< end` would exclude the recovery event occurring exactly at the end
timestamp.

---

## Task12

Count component and error-code combinations.

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^error=/)
      print $2, substr($i, 7)
  }
}' incident.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
1 worker DB_TIMEOUT
1 edge DB_TIMEOUT
1 edge APP_UNAVAILABLE
1 db DB_TIMEOUT
```

Observed error distribution:

```text
DB_TIMEOUT
→ edge
→ db
→ worker

APP_UNAVAILABLE
→ edge
```

This showed that `DB_TIMEOUT` appeared across several components rather than
within only the database component.

---

## Notes

### Identifier Transition

A single event chain may require different correlation identifiers at
different stages.

```text
client IP
→ login account
→ sudo user
→ service name
```

For this dataset:

```text
192.0.2.44
→ deploy
→ sudo command
→ app.service restart
```

This is more powerful than searching the entire incident using only one
identifier.

---

### Same Username Does Not Mean Same Session

This search:

```bash
grep 'user=deploy' incident.log
```

returned events involving different source IPs.

```text
198.51.100.30
192.0.2.44
```

The shared username alone was insufficient to prove that all events belonged
to the same session.

Correlation should consider:

```text
identifier
+
time
+
event sequence
+
component relationships
```

---

### Inclusive and Exclusive Time Boundaries

Use an exclusive end boundary for adjacent windows:

```awk
$1 >= start && $1 < end
```

This prevents overlap.

Use an inclusive end boundary when the final event itself must be included:

```awk
$1 >= start && $1 <= end
```

The correct operator depends on the investigation question.

---

### ISO 8601 String Comparison

String comparison worked because timestamps had:

```text
year-first ordering
fixed-width numeric fields
identical timezone offsets
identical formatting
```

Example:

```text
2026-07-19T14:02:09+09:00
2026-07-19T14:02:46+09:00
```

Under these conditions:

```text
lexical order
=
chronological order
```

This assumption would require review if formats or timezone offsets differed.

---

### Status Distribution Versus Error Count

These are different operations.

Count distinct status groups:

```bash
extract_status |
sort |
uniq -c |
wc -l
```

This counts the number of status-code categories.

Count events whose status is at least 500:

```awk
if (status_code >= 500)
  error_count++
```

The first asks:

```text
How many kinds of status codes occurred?
```

The second asks:

```text
How many server-error events occurred?
```

---

### Event Count Versus Unique Component Count

These are also different metrics.

Count all `DB_TIMEOUT` events:

```text
3
```

Count unique components containing `DB_TIMEOUT`:

```text
edge
db
worker
→ 3 components
```

The numbers happened to be equal in this dataset, but they measure different
properties.

---

### Observation and Causality

Observed:

```text
service_stopped
→ upstream_failure
→ service_started
```

Reasonable interpretation:

```text
The failure may be related to the restart interval.
```

Unsupported conclusion:

```text
The restart definitely caused every observed failure.
```

Incident analysis should maintain this boundary:

```text
timeline
→ correlation
→ hypothesis
→ further evidence
```

---

## Common Mistakes

### Mistake 1

Using an incomplete boolean condition.

Incorrect:

```awk
$3 == "WARN" || "ERROR"
```

Correct:

```awk
$3 == "WARN" || $3 == "ERROR"
```

---

### Mistake 2

Providing a filename after piping filtered input.

Incorrect:

```bash
awk 'filter' incident.log |
awk 'extract' incident.log
```

The second `awk` reads the file directly and ignores the filtered standard
input.

Correct:

```bash
awk 'filter' incident.log |
awk 'extract'
```

Better when practical:

```bash
awk 'filter {
  extract
}' incident.log
```

---

### Mistake 3

Using lexical reverse sorting for numeric counts.

Incorrect:

```bash
sort -r
```

Correct:

```bash
sort -nr
```

---

### Mistake 4

Removing the count from a ranked result.

Incomplete:

```bash
... |
head -n1 |
awk '{print $2}'
```

This retains only the value.

When the report requires both count and value, preserve the entire
`head -n1` record.

---

### Mistake 5

Counting distinct status codes instead of error events.

Incorrect pattern:

```bash
extract_status |
sort |
uniq -c |
wc -l
```

Correct event count:

```awk
if (status_code >= 500)
  error_count++
```

---

### Mistake 6

Counting `DB_TIMEOUT` occurrences instead of unique affected components.

Occurrence count:

```text
number of DB_TIMEOUT events
```

Required metric:

```text
number of unique components containing DB_TIMEOUT
```

Correct pattern:

```bash
extract_component_for_DB_TIMEOUT |
sort -u |
wc -l
```

---

### Mistake 7

Using an exclusive boundary when the recovery event must be included.

Incorrect:

```awk
$1 < end
```

when the recovery event occurs exactly at `end`.

Correct:

```awk
$1 <= end
```

---

## Reusable Patterns

Extract an unordered `key=value` field:

```bash
awk 'condition {
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^key=/)
      print substr($i, length("key=") + 1)
  }
}' file
```

Find values common to two event classes:

```bash
comm -12 \
  <(first_event_pipeline | sort -u) \
  <(second_event_pipeline | sort -u)
```

Filter an ISO 8601 time window:

```bash
awk \
  -v start='START_TIMESTAMP' \
  -v end='END_TIMESTAMP' \
  '$1 >= start && $1 < end' \
  file
```

Include the exact end event:

```bash
awk \
  -v start='START_TIMESTAMP' \
  -v end='END_TIMESTAMP' \
  '$1 >= start && $1 <= end' \
  file
```

Count status codes within a time window:

```bash
awk \
  -v start='START_TIMESTAMP' \
  -v end='END_TIMESTAMP' \
  '$1 >= start && $1 < end && $2 == "component" {
    for (i = 1; i <= NF; i++)
      if ($i ~ /^status=/)
        print substr($i, length("status=") + 1)
  }' file |
sort |
uniq -c |
sort -nr
```

Count server-error events:

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^status=/) {
      status_code = substr($i, length("status=") + 1) + 0

      if (status_code >= 500)
        error_count++
    }
  }
}
END {
  print error_count + 0
}' file
```

Count unique affected components for one error:

```bash
awk '{
  for (i = 1; i <= NF; i++)
    if ($i == "error=TARGET_ERROR")
      print $2
}' file |
sort -u |
wc -l
```

---

## Final Mental Model

Incident reconstruction is not only aggregation.

It is the controlled connection of evidence across components.

```text
raw events
→ classify by component and event
→ extract stable identifiers
→ correlate shared identifiers
→ transition to new identifiers when necessary
→ narrow the time window
→ compare system state before and after
→ distinguish evidence from explanation
```

The event chain in this session was:

```text
repeated login failures from one client
→ rate limit
→ successful deploy login
→ sudo service-restart command
→ application restart
→ temporary upstream failure
```

A separate correlated chain was:

```text
edge DB_TIMEOUT
→ db connection timeout
→ worker DB_TIMEOUT
→ db recovery
```

The most important reasoning rule was:

```text
correlation is evidence of relationship

correlation alone is not proof of cause
```

The shell pipeline supports the investigation by transforming raw log text
into:

```text
counts
identifiers
time windows
cross-component timelines
```

The final conclusion must remain limited to what those observations support.
