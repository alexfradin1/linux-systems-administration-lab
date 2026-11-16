# Linux File Ownership and Permissions

## Objective

Configure a shared directory that permits authorized group members to collaborate while preventing access by unrelated users.

## Shared Directory

Path:

`/srv/labteam`

Owner: `user`

Group: `labteam`

## Configuration Commands

```bash
sudo mkdir -p /srv/labteam
sudo chown user:labteam /srv/labteam
sudo chmod 2770 /srv/labteam
```

Verify:

```bash
ls -ld /srv/labteam
```

## Permission Breakdown

Expected directory permissions:

```text
drwxrws---
```

| Component | Meaning |
|---|---|
| `d` | Directory |
| `rwx` | Owner can read, write, and enter |
| `rws` | Group can read, write, and enter; setgid enabled |
| `---` | Others have no access |

The leading `2` in permission mode `2770` enables the directory setgid bit.

Setgid causes newly created files and subdirectories to inherit the directory's group ownership.

It does not automatically guarantee group-write access on new files.

## Initial File Test

The `labtech` account created:

`/srv/labteam/labtech-test.txt`

The initial file permissions were:

```text
-rw-r--r--
```

The administrator could read the file but initially could not modify it without elevated privileges.

Group-write access was enabled using:

```bash
sudo chmod g+rw /srv/labteam/labtech-test.txt
```

The file was subsequently secured with:

```bash
sudo chmod 660 /srv/labteam/labtech-test.txt
```

Result:

```text
-rw-rw----
```

## Group Membership Issue

Despite correct file permissions, the existing administrator shell initially lacked active membership in `labteam`.

After refreshing the group credentials using `newgrp labteam`, the administrator successfully appended text without `sudo`.

## Unauthorized Access Test

The `outsider` account attempted to enter the shared directory and read the protected file.

Both operations returned:

```text
Permission denied
```

## Conclusion

Directory ownership, setgid permissions, and group-based file access successfully restricted unauthorized access.

The lab also demonstrated why active process credentials must be verified when troubleshooting permission failures.
