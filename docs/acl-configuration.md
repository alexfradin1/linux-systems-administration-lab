# Linux Access Control Lists (ACLs)

## Objective

Configure default ACLs so newly created files inside the shared directory inherit appropriate group permissions.

## Background

Traditional Linux permissions provide access settings for the owner, owning group, and others.

Access Control Lists provide additional flexibility, including named user and group permissions and default permissions inherited by new filesystem objects.

## Configuration

Shared directory:

`/srv/labteam`

Commands:

```bash
sudo setfacl -m g:labteam:rwx /srv/labteam
sudo setfacl -d -m g:labteam:rwx /srv/labteam
```

The first command configures an access ACL on the directory.

The second configures a default ACL inherited by newly created files and subdirectories.

## Verification

```bash
getfacl /srv/labteam
```

The configuration showed:

```text
group:labteam:rwx
mask::rwx
default:group:labteam:rwx
default:mask::rwx
default:other::---
```

## Inheritance Test

The `labtech` account created:

```bash
echo "ACL inheritance test" > /srv/labteam/acl-test.txt
```

The new file inherited an ACL entry for `labteam`.

Relevant output:

```text
group:labteam:rwx    #effective:rw-
mask::rw-
other::---
```

The effective permissions permitted reading and writing without granting execute permission to the regular file.

## Authorized Write Test

The administrator activated the `labteam` group using:

```bash
newgrp labteam
```

Then executed:

```bash
echo "Verified by user" >> /srv/labteam/acl-test.txt
```

The write operation succeeded without sudo.

The file contained:

```text
ACL inheritance test
Verified by user
```

## Unauthorized Access Test

The `outsider` account attempted:

```bash
cat /srv/labteam/acl-test.txt
```

Result:

```text
Permission denied
```

## Conclusion

The default ACL configuration successfully enabled group-based collaboration on newly created files while preserving restrictions against unauthorized users.

The test demonstrated both permission inheritance and effective access enforcement.
