# Linux Administration Troubleshooting Report

## Overview

This report documents issues encountered during the Linux Users & Permissions lab, including symptoms, investigation, root causes, corrective actions, and verification.

## Incident 1 — Incorrect Ownership Command

**Issue:** Directory ownership was unintentionally modified.

### Symptoms

The shared directory displayed an unexpected numeric owner:

```text
drwxr-xr-x 2 2770 labteam ...
```

### Root Cause

The following command was executed:

```bash
sudo chown 2770 /srv/labteam
```

The intended command was:

```bash
sudo chmod 2770 /srv/labteam
```

`chown` changes ownership, while `chmod` changes permission modes.

### Resolution

```bash
sudo chown user:labteam /srv/labteam
sudo chmod 2770 /srv/labteam
```

### Verification

```bash
ls -ld /srv/labteam
```

The directory was restored to the intended owner, group, and permissions.

### Lesson Learned

A command can complete successfully while producing an unintended system configuration. Verify changes immediately after performing administrative operations.

---

## Incident 2 — Stale Group Membership

**Issue:** An authorized account could not write to a group-writable file.

### Symptoms

The administrator account received:

```text
Permission denied
```

The file had group-write permission, and the account database listed `user` as a member of `labteam`.

### Investigation

Running:

```bash
id user
```

showed the configured group membership.

However:

```bash
id
```

did not initially include `labteam` in the current shell's active group list.

### Root Cause

The administrator's existing desktop session had not acquired the updated supplementary group membership.

### Resolution

The `newgrp` utility was initially unavailable.

Ubuntu suggested installing:

```bash
sudo apt install util-linux-extra
```

After installation:

```bash
newgrp labteam
```

started a shell with `labteam` as the active group.

### Verification

The administrator successfully executed:

```bash
echo "Updated by user" >> /srv/labteam/labtech-test.txt
```

without sudo.

### Lesson Learned

Configured group membership and the active credentials of an existing process can differ.

Always verify the credentials of the current session when diagnosing access problems.

---

## Incident 3 — Group Name Typographical Error

**Issue:** Adding a user to a supplementary group initially failed.

### Symptoms

The `usermod` command reported that the specified group did not exist.

### Root Cause

The group name was entered incorrectly.

### Resolution

The command was corrected to:

```bash
sudo usermod -aG labteam labtech
```

### Verification

```bash
getent group labteam
```

returned:

```text
labteam:x:1002:user,labtech
```

### Lesson Learned

Linux command-line tools require exact resource names. Error messages and verification commands should guide troubleshooting.

---

## Final Outcome

All final ACL inheritance and access-control tests passed.

The project demonstrated successful troubleshooting of ownership mistakes, permission failures, stale group credentials, and command-entry errors.

The primary operational lesson is to validate both the intended configuration and the observed system behavior before declaring a task complete.
