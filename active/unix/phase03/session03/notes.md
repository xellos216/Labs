# Session03 Notes

## Core Idea

Service logs often combine two different structures.

The beginning of each record may use stable positional fields:

```text
timestamp component level event
```

The remaining fields may use unordered `key=value` pairs:

```text
request_id=req-1001
duration_ms=12
status=200
error=DB_TIMEOUT
queue_depth=31
```

The main parsing strategy was:

```text
stable prefix
→ fixed field access

unordered key=value data
→ scan every field
→ find a semantic prefix
→ extract the associated value
```

The session focused on:

```text
log classification
→ structured metric extraction
→ aggregation
→ maximum tracking
→ timeline analysis
→ compact report generation
```

---

## Task1

Count events by log level.

```bash
awk '{print $3}' service.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
12 INFO
6 WARN
6 ERROR
```

Pattern:

```text
extract level
→ group identical values
→ count
→ rank by frequency
```

This is preferable to reading the file separately for each known level.

A new level would be included automatically.

---

## Task2

Count events by component.

```bash
awk '{print $2}' service.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
11 api
7 worker
6 db
```

The prefix fields were stable:

```text
$1 = timestamp
$2 = component
$3 = level
$4 = event name
```

---

## Task3

Count event names.

```bash
awk '{print $4}' service.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
6 request_completed
3 request_failed
3 query_completed
2 queue_delay
2 job_failed
2 job_completed
1 slow_request
1 slow_query
1 rate_limit
1 pool_pressure
1 job_started
1 connection_timeout
```

Pattern:

```text
event-name field
→ aggregation
→ event frequency
```

---

## Task4

Extract every `duration_ms` value.

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^duration_ms=/)
      print substr($i, 13)
  }
}' service.log
```

Dataset output:

```text
12
4
85
640
590
1200
1800
95
220
980
48
1400
15
1500
310
110
52
11
```

The field position was not fixed.

Examples:

```text
duration_ms=1500 error=PAYMENT_TIMEOUT
duration_ms=110 method=GET
duration_ms=52 query=order_lookup
```

The parser therefore searched every field.

```awk
$i ~ /^duration_ms=/
```

identified the correct field.

`duration_ms=` contains 12 characters, so:

```awk
substr($i, 13)
```

returned only the numeric part.

---

## Task5

Find the event with the largest `duration_ms`.

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^duration_ms=/) {
      duration = substr($i, 13) + 0

      if (duration > max_duration) {
        max_duration = duration
        max_line = $0
      }
    }
  }
}
END {
  print max_line
}' service.log
```

Dataset result:

```text
2026-07-18T10:02:14+09:00 worker ERROR job_failed job_id=job-702 queue=default error=DB_TIMEOUT attempts=3 duration_ms=1800
```

Pattern:

```text
extract metric
→ convert to number
→ compare with current maximum
→ save metric and owning record
→ print saved record at END
```

The metric and its owner must be preserved together.

Incorrect mental model:

```text
save maximum value only
→ original event is lost
```

Correct mental model:

```text
save maximum value
+
save the record that produced it
```

---

## Task6

Calculate average `duration_ms`.

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^duration_ms=/) {
      duration = substr($i, 13) + 0
      duration_count++
      duration_sum += duration
    }
  }
}
END {
  if (duration_count > 0)
    print duration_sum / duration_count
}' service.log
```

Dataset values:

```text
duration events: 18
duration sum: 9072
average: 504
```

Pattern:

```text
extract numeric metric
→ accumulate sum
→ count matching records
→ divide sum by count
```

The `duration_count > 0` check prevents division by zero on inputs without
duration metrics.

---

## Task7

Count error codes.

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^error=/)
      print substr($i, 7)
  }
}' service.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
2 UPSTREAM_UNAVAILABLE
2 DB_TIMEOUT
1 PAYMENT_TIMEOUT
1 CONN_TIMEOUT
```

`error=` contains six characters, so:

```awk
substr($i, 7)
```

returned only the error code.

Pattern:

```text
find key=value field
→ remove key prefix
→ group values
→ rank values
```

---

## Task8

Find the event with the largest queue depth.

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^queue_depth=/) {
      queue_depth = substr($i, 13) + 0

      if (queue_depth > max_depth) {
        max_depth = queue_depth
        max_line = $0
      }
    }
  }
}
END {
  print max_line
}' service.log
```

Dataset result:

```text
2026-07-18T10:04:03+09:00 worker WARN queue_delay queue=critical queue_depth=31 wait_ms=760
```

This reused the same metric-owner maximum pattern as the duration task.

```text
metric discovery
→ numeric conversion
→ maximum comparison
→ owner preservation
```

---

## Task9

Count `ERROR` events by component.

```bash
awk '$3 == "ERROR" {print $2}' service.log |
sort |
uniq -c |
sort -nr
```

Dataset result:

```text
3 api
2 worker
1 db
```

The level field was already known to be `$3`.

Scanning every field for `ERROR` was unnecessary.

The structural condition was:

```text
$3 == "ERROR"
```

not:

```text
some field begins with ERROR
```

Also, the log level and the `error=` metric are different concepts.

```text
ERROR
→ event severity level

error=DB_TIMEOUT
→ structured error code
```

They happened to occur together in this dataset, but they are not
interchangeable.

---

## Task10

Aggregate `ERROR` events by minute.

```bash
awk '$3 == "ERROR" {
  print substr($1, 12, 5)
}' service.log |
sort |
uniq -c
```

Dataset result:

```text
3 10:02
1 10:03
1 10:04
1 10:05
```

The timestamp field had this form:

```text
2026-07-18T10:02:02+09:00
```

The expression:

```awk
substr($1, 12, 5)
```

extracted:

```text
10:02
```

Pattern:

```text
ERROR events
→ normalize timestamps to minute buckets
→ sort chronologically
→ count events per minute
```

No final `sort -nr` was used because the objective was a timeline.

---

## Integrated Report

```bash
total_events=$(wc -l < service.log)

error_events=$(
  awk '$3 == "ERROR"' service.log |
  wc -l
)

average_duration=$(
  awk '{
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^duration_ms=/) {
        duration = substr($i, 13) + 0
        duration_count++
        duration_sum += duration
      }
    }
  }
  END {
    if (duration_count > 0)
      print duration_sum / duration_count
  }' service.log
)

slowest_event=$(
  awk '{
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^duration_ms=/) {
        duration = substr($i, 13) + 0

        if (duration > max_duration) {
          max_duration = duration
          max_line = $0
        }
      }
    }
  }
  END {
    print max_duration, max_line
  }' service.log
)

max_queue_depth=$(
  awk '{
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^queue_depth=/) {
        queue_depth = substr($i, 13) + 0

        if (queue_depth > max_depth) {
          max_depth = queue_depth
          max_line = $0
        }
      }
    }
  }
  END {
    print max_depth, max_line
  }' service.log
)

top_error_code=$(
  awk '{
    for (i = 1; i <= NF; i++) {
      if ($i ~ /^error=/)
        print substr($i, 7)
    }
  }' service.log |
  sort |
  uniq -c |
  sort -nr |
  head -n1
)

peak_error_minute=$(
  awk '$3 == "ERROR" {
    print substr($1, 12, 5)
  }' service.log |
  sort |
  uniq -c |
  sort -nr |
  head -n1
)

printf '%s\n' \
  "Total events: $total_events" \
  "ERROR events: $error_events" \
  "Average duration_ms: $average_duration" \
  "Slowest event: $slowest_event" \
  "Max queue depth: $max_queue_depth" \
  "Top error code: $top_error_code" \
  "Peak error minute: $peak_error_minute"
```

Dataset result:

```text
Total events: 24
ERROR events: 6
Average duration_ms: 504
Slowest event: 1800 2026-07-18T10:02:14+09:00 worker ERROR job_failed job_id=job-702 queue=default error=DB_TIMEOUT attempts=3 duration_ms=1800
Max queue depth: 31 2026-07-18T10:04:03+09:00 worker WARN queue_delay queue=critical queue_depth=31 wait_ms=760
Top error code: 2 UPSTREAM_UNAVAILABLE
Peak error minute: 3 10:02
```

`DB_TIMEOUT` and `UPSTREAM_UNAVAILABLE` were tied with two events each.

Either may appear first unless an explicit secondary sort key is defined.

---

## Notes

### Fixed Fields and Dynamic Fields

Fixed fields were appropriate for the stable prefix.

```text
$2 → component
$3 → level
$4 → event name
```

Dynamic scanning was appropriate for unordered metrics.

```text
duration_ms=
error=
queue_depth=
```

Mental model:

```text
stable record position
→ direct field access

unordered structured attribute
→ scan fields for key prefix
```

---

### Prefix Matching

This condition:

```awk
$i ~ /^error=/
```

means:

```text
the current field begins with error=
```

The `^` anchor matters.

Without it:

```awk
$i ~ /error=/
```

would also match a field containing `error=` somewhere in the middle.

---

### Prefix Length and `substr()`

Examples:

```text
duration_ms=
→ 12 characters
→ value begins at character 13

queue_depth=
→ 12 characters
→ value begins at character 13

error=
→ 6 characters
→ value begins at character 7
```

Hard-coded offsets are valid when the key string is fixed.

A more reusable form is:

```awk
substr($i, length("duration_ms=") + 1)
```

Example:

```awk
if ($i ~ /^duration_ms=/)
  print substr($i, length("duration_ms=") + 1)
```

---

### Numeric Conversion

This expression explicitly converts extracted text to a number:

```awk
duration = substr($i, 13) + 0
```

The conversion is useful before:

```text
numeric comparison
addition
average calculation
ranking
```

Mental model:

```text
parsed text
→ explicit numeric value
→ numeric operation
```

---

### Maximum Ranking

Correct pattern:

```awk
if (metric > max_metric) {
  max_metric = metric
  max_line = $0
}
```

Then:

```awk
END {
  print max_metric, max_line
}
```

The maximum metric and the record that owns it must be updated in the same
condition.

---

### Timeline Versus Ranking

Timeline output:

```bash
sort |
uniq -c
```

Frequency ranking:

```bash
sort |
uniq -c |
sort -nr
```

Selecting the first timeline row does not necessarily select the busiest
time bucket.

Incorrect maximum selection:

```bash
sort |
uniq -c |
head -n1
```

Correct maximum selection:

```bash
sort |
uniq -c |
sort -nr |
head -n1
```

---

### Log Level Versus Error Attribute

These are different structures:

```text
api ERROR request_failed ...
```

```text
error=DB_TIMEOUT
```

`ERROR` is a severity field.

`error=DB_TIMEOUT` is a structured attribute.

A record can theoretically contain:

```text
ERROR without error=
```

or:

```text
WARN with error=
```

Therefore the correct parser depends on the question being asked.

---

## Common Mistakes

### Mistake 1

Reading the file once per known category.

Example:

```bash
info_count=$(awk '$3 == "INFO"' service.log | wc -l)
warn_count=$(awk '$3 == "WARN"' service.log | wc -l)
error_count=$(awk '$3 == "ERROR"' service.log | wc -l)
```

This works, but it requires known categories and repeated file scans.

More general:

```bash
awk '{print $3}' service.log |
sort |
uniq -c
```

---

### Mistake 2

Leaving a shell quote open.

Incomplete:

```bash
awk '$3 == "ERROR
```

zsh displayed a continuation prompt because the single-quoted program was
not closed.

Recovery:

```text
Ctrl-C
```

Then enter the complete command.

---

### Mistake 3

Printing only the maximum value.

```bash
extract_duration |
awk '$1 > max {max = $1} END {print max}'
```

This finds the largest number but loses the original event.

Correct:

```text
save metric
+
save original record
```

---

### Mistake 4

Updating the owner outside the maximum condition.

Incorrect:

```awk
if (duration > max)
  max = duration

line = $0
```

`line` is updated for every matching record and ends as the final record.

Correct:

```awk
if (duration > max) {
  max = duration
  line = $0
}
```

---

### Mistake 5

Printing `$0` in `END`.

Incorrect:

```awk
END {
  print $0
}
```

At `END`, `$0` refers to the last processed record.

Correct:

```awk
END {
  print saved_line
}
```

---

### Mistake 6

Filtering ERROR events through arbitrary field scanning.

Overcomplicated:

```awk
for (i = 1; i <= NF; i++)
  if ($i ~ /^ERROR/)
    print $0
```

Direct structural condition:

```awk
$3 == "ERROR" {print $2}
```

---

### Mistake 7

Keeping unnecessary fields and removing them later.

Overcomplicated:

```text
print component and level
→ aggregate
→ remove level
```

Direct:

```text
filter by level
→ print component only
→ aggregate
```

---

### Mistake 8

Using the earliest minute as the peak minute.

Incorrect:

```bash
sort |
uniq -c |
head -n1
```

Correct:

```bash
sort |
uniq -c |
sort -nr |
head -n1
```

---

### Mistake 9

Printing the maximum queue event without the metric.

Incomplete:

```awk
END {
  print max_line
}
```

Required report shape:

```awk
END {
  print max_depth, max_line
}
```

---

## Reusable Patterns

Count values from a fixed field:

```bash
awk '{print $field}' file |
sort |
uniq -c |
sort -nr
```

Filter by one fixed field and extract another:

```bash
awk '$condition_field == "value" {
  print $target_field
}' file
```

Extract a `key=value` metric:

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^key=/)
      print substr($i, length("key=") + 1)
  }
}' file
```

Find a maximum metric and preserve its record:

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^key=/) {
      metric = substr($i, length("key=") + 1) + 0

      if (metric > max_metric) {
        max_metric = metric
        max_line = $0
      }
    }
  }
}
END {
  print max_metric, max_line
}' file
```

Calculate an average:

```bash
awk '{
  for (i = 1; i <= NF; i++) {
    if ($i ~ /^key=/) {
      metric = substr($i, length("key=") + 1) + 0
      metric_sum += metric
      metric_count++
    }
  }
}
END {
  if (metric_count > 0)
    print metric_sum / metric_count
}' file
```

Aggregate by minute:

```bash
awk 'condition {
  print substr($timestamp_field, start, length)
}' file |
sort |
uniq -c
```

Find the busiest minute:

```bash
extract_minutes |
sort |
uniq -c |
sort -nr |
head -n1
```

---

## Final Mental Model

A service log can be interpreted as two connected layers.

```text
stable event envelope
+
unordered structured attributes
```

The envelope identifies:

```text
when
which component
severity
event type
```

The attributes describe:

```text
request identity
status
duration
error code
queue state
resource state
```

The complete analysis flow is:

```text
raw service events
→ classify by stable fields
→ scan dynamic key=value attributes
→ convert extracted values to correct types
→ aggregate metrics
→ preserve metric ownership
→ construct timelines
→ generate operational report
```

The central lesson was:

```text
do not assume every useful value has a permanent field number

instead:

identify whether the value belongs to
a stable positional prefix
or
an unordered key=value section
```

Observability becomes useful when the parser preserves both:

```text
the metric
and
the event that produced it
```
