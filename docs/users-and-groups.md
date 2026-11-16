# Linux User and Group Management

## Objective

Configure separate administrative, authorized standard, and unauthorized test accounts to demonstrate least-privilege access control.

## Environment

- Operating system: Ubuntu Desktop
- Virtualization: Oracle VirtualBox
- Administrator: `user`
- Authorized standard user: `labtech`
- Unauthorized test user: `outsider`
- Shared group: `labteam` (GID 1002)

## Account Configuration

The `user` account belongs to the `sudo` group and can perform administrative operations.

The `labtech` account is a standard account without membership in `sudo`.

The `outsider` account was created to test whether access restrictions prevent unauthorized users from reading shared resources.

## Commands Used

Create standard user accounts:

```bash
sudo adduser labtech
sudo adduser outsider
```

Create the shared group:

```bash
sudo groupadd labteam
```

Add authorized accounts without replacing their existing supplementary groups:

```bash
sudo usermod -aG labteam user
sudo usermod -aG labteam labtech
```

Verify membership:

```bash
getent group labteam
id user
id labtech
id outsider
```

## Verified Results

The group database returned:

```text
labteam:x:1002:user,labtech
```

`user` retained its `sudo` membership.

`labtech` belonged to `labteam` without becoming a sudo administrator.

`outsider` belonged to its own primary group and the `users` group, but not `labteam`.

## Security Principle

The principle of least privilege requires granting users only the access necessary for their responsibilities.

In this lab, `labtech` could collaborate through `labteam` without receiving administrator privileges.

## Conclusion

Linux user and group management successfully established distinct administrative and standard-user roles, providing the identity foundation for the subsequent file permission and ACL tests.
