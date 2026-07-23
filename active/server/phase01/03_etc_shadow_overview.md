# Linux Server Administration
## Phase 01 — Session 03
# /etc/shadow Overview

## Metadata

```yaml
Roadmap: Linux Server Administration
Phase: 01
Session: 03
Title: /etc/shadow Overview
Status: Completed
Review: Pending
ArchiveVersion: 3
Date: 2026-07-24
```

---

# Objective

Understand how Linux stores password authentication state and password-aging
policy in `/etc/shadow`.

The session examined the nine-field shadow record, distinguished raw password
field values from the summaries produced by `passwd` and `chage`, and verified
the structural relationship between `/etc/passwd` and `/etc/shadow` without
revealing password hashes or modifying account state.

---

# Learning Summary

Linux separates broadly readable identity information from sensitive password
authentication data.

```text
/etc/passwd
→ user name
→ UID and primary GID
→ home directory
→ login program
→ generally readable identity attributes

/etc/shadow
→ password hash or password lock state
→ password-aging policy
→ account-expiration data
→ restricted read access
```

The second field of `/etc/passwd` contained `x`, indicating that the associated
password information was maintained separately in the shadow database.

A shadow record contained nine colon-separated fields:

```text
username:password:last-change:min:max:warn:inactive:expire:reserved
```

The regular account record was inspected with the password field redacted:

```text
username: labuser
password field: [REDACTED]
last change: 20647
minimum age: 0
maximum age: 99999
warning: 7
inactive:
expire:
reserved:
field count: 9
```

The value `20647` represented the number of days since the Unix epoch. It
converted to:

```text
2026-07-13
```

The same account state was exposed through three different interfaces:

```text
/etc/shadow
→ raw stored fields

passwd -S
→ concise password-state and aging summary

chage -l
→ human-readable aging and expiration summary
```

These interfaces presented the same underlying account policy from different
administrative perspectives.

The password field for the regular account contained a usable hash. Only its
modular crypt identifier was inspected:

```text
hash identifier: y
```

The `$y$` identifier indicated a yescrypt-formatted password hash. The hash
itself was never displayed or preserved.

The complete shadow database contained:

```text
hash-present: 1
locked: 15
disabled: 18
```

A more direct first-character count produced:

```text
starts-with-!: 15
starts-with-*: 18
empty: 0
other: 1
total: 34
```

The one `other` record was the usable modular password hash. The remaining
records were password-locked or configured with unusable password values.

The session established an important separation:

```text
password authentication state
≠
account existence
≠
ability to run a process under the account's UID
```

A service account may have a locked password while remaining a valid identity
for service isolation and process execution.

---

# Key Concepts

- shadow password database
- restricted authentication data
- nine-field shadow record
- password hash
- modular crypt format
- yescrypt identifier
- password lock state
- unusable password value
- empty password field
- last password change
- minimum password age
- maximum password age
- expiration warning period
- password inactivity period
- account expiration
- Unix epoch day count
- `passwd -S`
- `chage -l`
- structural validation
- user-name set consistency
- duplicate shadow records
- process substitution
- privilege boundary
- password authentication
- SSH public-key authentication
- service account identity

---

# Practical Observations

## Observation 1 — The Nine Shadow Fields

The regular account's shadow record was inspected without displaying its
password field.

```text
Prediction
↓

The record should contain a user name, password state, password-change date,
aging values, inactivity policy, account expiration, and a reserved field.

↓

Observation
↓

username: labuser
password field: [REDACTED]
last change: 20647
minimum age: 0
maximum age: 99999
warning: 7
inactive:
expire:
reserved:
field count: 9

↓

Explanation
↓

Each shadow record used nine colon-separated fields. Empty fields still
occupied their positions and represented unset policy values rather than
missing columns.
```

The fields were:

```text
1  user name
2  encrypted password or password-state value
3  last password change, in days since 1970-01-01
4  minimum days before another password change
5  maximum password lifetime
6  warning days before password expiration
7  inactive days after password expiration
8  account expiration date, in epoch days
9  reserved field
```

---

## Observation 2 — Converting the Epoch-Day Value

The raw last-change value was converted into a calendar date.

```bash
date -d '1970-01-01 + 20647 days' '+%F'
```

Output:

```text
2026-07-13
```

```text
shadow last-change field
→ integer day count

date conversion
→ human-readable calendar date
```

The raw field stored a stable numeric representation rather than a formatted
date string.

---

## Observation 3 — Human-Readable Aging Policy

The account policy was inspected with:

```bash
sudo chage -l labuser
```

Observed output:

```text
Last password change                                    : Jul 13, 2026
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```

```text
raw shadow numbers
↓
chage interpretation
↓
administrative policy summary
```

`chage -l` translated the raw shadow fields into dates and explicit `never`
states.

---

## Observation 4 — Safe Password-Field Classification

The regular account's password field was classified without displaying the
hash.

Observed result:

```text
password state: hash present
password field length: 73
```

Only the modular crypt identifier was then extracted:

```text
hash identifier: y
```

```text
$y$
→ yescrypt-format identifier

remaining password field
→ salt and derived hash data
→ sensitive and not archived
```

A hash being present meant that a password verifier existed. It did not prove
that every login mechanism was configured to permit password authentication.

---

## Observation 5 — Shadow Password-State Distribution

All shadow password fields were classified without printing their contents.

Observed summary:

```text
hash-present: 1
locked: 15
disabled: 18
```

A first-character count confirmed the distribution:

```text
starts-with-!: 15
starts-with-*: 18
empty: 0
other: 1
total: 34
```

The meanings were:

```text
hash-form value
→ potentially usable password verifier

! at the beginning
→ password-locked form
→ may preserve an older hash after the marker

*
→ value that cannot be produced by a normal password hash comparison
→ password authentication disabled for that record

empty field
→ no stored password value
→ resulting login behavior depends on the authentication stack and policy
```

The `*` value was not a usable password.

---

## Observation 6 — `/etc/passwd` and `/etc/shadow` User Sets

An initial attempt tried to compare the two user-name sets with `comm` and
process substitution:

```bash
sudo LC_ALL=C comm -3 \
  <(cut -d: -f1 /etc/passwd | LC_ALL=C sort) \
  <(cut -d: -f1 /etc/shadow | LC_ALL=C sort)
```

It failed:

```text
cut: /etc/shadow: Permission denied
comm: /dev/fd/63: No such file or directory
```

The failure occurred because the current shell created and executed the
process substitutions before `sudo` started `comm`.

```text
current unprivileged shell
↓
runs <(...) commands
↓
cut attempts to read /etc/shadow
↓
permission denied

sudo
↓
applies only to comm
```

The comparison was replaced with one privileged `awk` process:

```bash
sudo awk -F: '
NR == FNR {
  passwd_users[$1] = 1
  next
}
{
  shadow_users[$1] = 1
}
END {
  for (user in passwd_users)
    if (!(user in shadow_users))
      print "passwd only:", user

  for (user in shadow_users)
    if (!(user in passwd_users))
      print "shadow only:", user
}' /etc/passwd /etc/shadow
```

Observed result:

```text
no output
```

The supported conclusion was:

```text
current /etc/passwd user-name set
=
current /etc/shadow user-name set
```

This verified name-set correspondence. It did not prove that every field in
every record was semantically correct.

---

## Observation 7 — Structural Field-Count Validation

Every shadow record was tested for exactly nine fields.

```bash
sudo awk -F: 'NF != 9 {
  print "invalid field count:", NR, NF, $1
}' /etc/shadow
```

Observed result:

```text
no output
```

```text
no validation output
→ every current record had NF = 9
```

This was evidence of structural consistency only.

It did not independently verify:

- whether each date was appropriate
- whether each password state was intended
- whether each policy value was secure
- whether each account should exist
- whether all cross-file relationships were correct

---

## Observation 8 — Duplicate Shadow User Check

Duplicate user names were checked with:

```bash
sudo awk -F: '
{
  user_count[$1]++
}
END {
  for (user in user_count)
    if (user_count[user] > 1)
      print "duplicate shadow user:", user
}' /etc/shadow
```

Observed result:

```text
no output
```

Therefore, every current shadow user name appeared once.

```text
one user name
→ one shadow record
```

A duplicated record would represent invalid or ambiguous account data. It
would be unsafe to depend on which duplicate a particular tool or library
might select.

---

## Observation 9 — `passwd -S` for a Regular Account

The regular account's concise password status was inspected:

```bash
sudo passwd -S labuser
```

Observed output:

```text
labuser P 2026-07-13 0 99999 7 -1
```

The fields meant:

```text
labuser
→ account name

P
→ usable password hash present

2026-07-13
→ last password change

0
→ minimum password age

99999
→ maximum password age

7
→ expiration warning period

-1
→ no configured post-expiration inactivity period
```

The status letters belonged to `passwd -S` output. They were not literal
values stored as the second shadow field.

Common `passwd -S` states included:

```text
P
→ usable password present

L
→ password locked

NP
→ no password
```

---

## Observation 10 — `passwd -S` for a Service Account

The `daemon` account was expected initially to have no password and report
`NP`.

The actual output was:

```bash
sudo passwd -S daemon
```

```text
daemon L 2026-04-20 0 99999 7 -1
```

```text
Prediction
↓

daemon may report NP because it is not used for interactive login.

↓

Observation
↓

daemon reported L.

↓

Explanation
↓

The account did not have an empty password field. Its password was locked.
A non-interactive service account and an account with no password are not the
same state.
```

Both accounts had similar aging values:

```text
labuser P 2026-07-13 0 99999 7 -1
daemon  L 2026-04-20 0 99999 7 -1
```

The authentication difference came from the password-state field, not from the
shared aging values.

---

## Observation 11 — Password Lock Versus Process Execution

The `daemon` account remained a valid system identity despite its locked
password.

```text
daemon password state
→ L
→ password authentication unavailable

daemon UID and GID
→ still valid identity values

root or service manager
→ can create a process with those credentials
```

Password verification is one way to establish a login identity. It is not the
mechanism by which the kernel decides whether a privileged process may assign
a UID to another process.

This allows services to run under isolated accounts that cannot be used for
ordinary password login.

---

## Observation 12 — SSH Policy Versus Shadow State

The lab server permitted SSH administration through public-key authentication
while password authentication was disabled.

The regular account still had a password hash in `/etc/shadow`.

```text
shadow hash present
→ local password verifier exists

sshd authentication policy
→ determines whether SSH accepts password authentication

authorized public key
→ separate SSH authentication credential
```

Therefore:

```text
password hash present
+
SSH password login disabled
+
SSH public-key login enabled
```

was a consistent configuration.

A stored password hash does not force every authentication service to accept
passwords.

---

# Commands / Code

## Safely Display Shadow Fields Without the Hash

```bash
sudo awk -F: -v target_user='labuser' '
$1 == target_user {
  print "username:", $1
  print "password field: [REDACTED]"
  print "last change:", $3
  print "minimum age:", $4
  print "maximum age:", $5
  print "warning:", $6
  print "inactive:", $7
  print "expire:", $8
  print "reserved:", $9
  print "field count:", NF
}' /etc/shadow
```

Displays the selected record's structure without exposing its password field.

---

## Convert Epoch Days to a Date

```bash
date -d '1970-01-01 + 20647 days' '+%F'
```

Converts a shadow day count into a calendar date.

---

## Inspect Password Aging

```bash
sudo chage -l labuser
```

Displays password and account expiration policy in a human-readable form.

---

## Inspect Concise Password Status

```bash
sudo passwd -S labuser
sudo passwd -S daemon
```

Displays password state and aging values without printing password hashes.

---

## Safely Classify One Password Field

```bash
sudo awk -F: -v target_user='labuser' '
$1 == target_user {
  if ($2 == "")
    state = "empty"
  else if ($2 ~ /^!/)
    state = "locked"
  else if ($2 ~ /^\*/)
    state = "disabled"
  else
    state = "hash present"

  print "password state:", state
  print "password field length:", length($2)
}' /etc/shadow
```

Classifies the selected password field without displaying it.

---

## Extract Only the Modular Crypt Identifier

```bash
sudo awk -F: -v target_user='labuser' '
$1 == target_user && $2 ~ /^\$/ {
  split($2, parts, "$")
  print "hash identifier:", parts[2]
}' /etc/shadow
```

Prints only the algorithm identifier and not the salt or derived hash.

---

## Count Shadow Password States

```bash
sudo awk -F: '
{
  if ($2 == "")
    empty_count++
  else if ($2 ~ /^!/)
    locked_count++
  else if ($2 ~ /^\*/)
    disabled_count++
  else
    hash_count++
}
END {
  print "hash-present:", hash_count + 0
  print "locked:", locked_count + 0
  print "disabled:", disabled_count + 0
  print "empty:", empty_count + 0
  print "total:", NR
}' /etc/shadow
```

Summarizes password-field states without revealing their contents.

---

## Compare Passwd and Shadow User Names

```bash
sudo awk -F: '
NR == FNR {
  passwd_users[$1] = 1
  next
}
{
  shadow_users[$1] = 1
}
END {
  for (user in passwd_users)
    if (!(user in shadow_users))
      print "passwd only:", user

  for (user in shadow_users)
    if (!(user in passwd_users))
      print "shadow only:", user
}' /etc/passwd /etc/shadow
```

Reports user names that exist in only one of the two local databases.

---

## Validate the Shadow Field Count

```bash
sudo awk -F: 'NF != 9 {
  print "invalid field count:", NR, NF, $1
}' /etc/shadow
```

Reports records that do not contain exactly nine fields.

---

## Detect Duplicate Shadow User Names

```bash
sudo awk -F: '
{
  user_count[$1]++
}
END {
  for (user in user_count)
    if (user_count[user] > 1)
      print "duplicate shadow user:", user
}' /etc/shadow
```

Reports user names represented by more than one shadow record.

---

# Connections

```text
/etc/passwd
↓
public account identity attributes
↓
user name and UID/GID lookup
```

```text
/etc/shadow
↓
password verifier or lock state
↓
password-aging and expiration policy
```

```text
login or authentication service
↓
NSS account lookup
+
shadow password state
+
PAM or service-specific policy
↓
authentication decision
```

```text
successful authentication
↓
process credentials are established
↓
UID / GID / supplementary groups
↓
kernel access-control checks
```

```text
service manager running as root
↓
selects service UID and GID
↓
starts process under service-account credentials
↓
no interactive password login required
```

```text
SSH connection
↓
sshd authentication configuration
├─ public-key authentication
└─ password authentication
```

The existence of a local shadow hash and the authentication methods accepted by
`sshd` are related account-security controls, but they are not the same state.

This session extends the account-record model established during
`/etc/passwd` observation and prepares for the next session's examination of
primary and supplementary group membership.

---

# Common Misconceptions

## Incorrect

```text
/etc/passwd is the password-state database, while /etc/shadow only stores the
raw hash format.
```

Correct:

```text
/etc/passwd stores generally readable account identity attributes.
/etc/shadow stores the password verifier or lock state together with password
aging and account-expiration fields.
```

---

## Incorrect

```text
P and L are literal values found in the second field of /etc/shadow.
```

Correct:

```text
P, L, and NP are summary states produced by passwd -S. Raw shadow fields
contain hashes, lock markers, unusable values, or empty values.
```

---

## Incorrect

```text
A shadow password field beginning with * contains a usable password.
```

Correct:

```text
* is not a normal usable password hash. It prevents ordinary password
verification from succeeding.
```

---

## Incorrect

```text
A locked password means that the user account no longer exists.
```

Correct:

```text
Password lock state restricts password authentication. The account's user
name, UID, GID, ownership relationships, and possible process identity remain.
```

---

## Incorrect

```text
A service cannot run under an account whose password is locked.
```

Correct:

```text
A privileged process such as a service manager can start a process with the
service account's UID and GID without authenticating through that account's
password.
```

---

## Incorrect

```text
If SSH accepts only public keys, the local account cannot have a shadow
password hash.
```

Correct:

```text
The shadow hash is local account state. sshd separately decides which
authentication methods it will accept.
```

---

## Incorrect

```text
sudo placed before comm also grants root privileges to every <(...) process
substitution.
```

Correct:

```text
The current shell expands and starts process substitutions before executing
the sudo command. sudo applied to comm, not to the unprivileged cut process
that tried to read /etc/shadow.
```

---

## Incorrect

```text
No output from the nine-field check proves that every shadow record is
correct.
```

Correct:

```text
It proves only that every inspected record had nine fields. Semantic validity,
security policy, and cross-database correctness require additional checks.
```

---

## Incorrect

```text
When duplicate user records exist, it is safe to assume that the lower record
will always take priority.
```

Correct:

```text
Duplicate identity records represent an invalid and ambiguous database state.
Administrative decisions should not depend on which record a particular
lookup implementation happens to return.
```

---

# Key Takeaways

- `/etc/passwd` and `/etc/shadow` separate generally readable identity data from sensitive password state.
- A shadow record contains nine fields covering password state, aging, inactivity, and account expiration.
- The second shadow field may contain a usable hash, a lock marker, an unusable value, or an empty value.
- `P`, `L`, and `NP` are `passwd -S` summary states rather than raw shadow field values.
- Password expiration and account expiration are separate controls.
- `passwd -S`, `chage -l`, and `/etc/shadow` expose the same account policy in different forms.
- A locked password does not delete the account or prevent privileged software from using its UID.
- SSH public-key policy and the presence of a local password hash are separate authentication states.
- No-output validation commands support only the specific property they tested.
- Shell process substitutions do not automatically inherit the privilege of a later `sudo` command.
- Password hashes should be classified or redacted rather than copied into public learning archives.

---

# Review Questions

### Q1. Why does Linux separate `/etc/passwd` from `/etc/shadow` instead of storing all account information in one publicly readable file?

<details>
<summary>A</summary>

</details>

---

### Q2. What does each of the nine `/etc/shadow` fields represent, and which fields concern password aging rather than account identity?

<details>
<summary>A</summary>

</details>

---

### Q3. How do raw shadow values such as `!` and `*` differ from the `P`, `L`, and `NP` states displayed by `passwd -S`?

<details>
<summary>A</summary>

</details>

---

### Q4. What evidence showed that the current `/etc/shadow` file was structurally consistent, and what did that evidence not prove?

<details>
<summary>A</summary>

</details>

---

### Q5. Why did placing `sudo` before `comm` fail to grant permission to the `cut` process reading `/etc/shadow` inside process substitution?

<details>
<summary>A</summary>

</details>

---

### Q6. Why can a service process run under the `daemon` account even though `passwd -S daemon` reports `L`?

<details>
<summary>A</summary>

</details>

---

### Q7. Why is it not contradictory for an account to contain a usable shadow password hash while SSH password authentication is disabled?

<details>
<summary>A</summary>

</details>

---

### Q8. Suppose a password has expired but the account-expiration field has not been reached. How is that state different from an account whose account-expiration date has passed?

<details>
<summary>A</summary>

</details>

---

# Next Session

```text
Next:
Phase 01
Session 04

Topic:
Group Membership
```
