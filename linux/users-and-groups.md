# Users & Groups

## Users

### useradd
Create a new user.

```bash
useradd username                          # basic, no home directory
useradd -m username                       # create with home directory
useradd -m -s /bin/bash username          # set default shell
useradd -m -G sudo username               # add to sudo group on creation
```

### usermod
Modify an existing user.

```bash
usermod -aG sudo username       # add user to sudo group (a = append, G = group)
usermod -s /bin/bash username   # change shell
usermod -l newname oldname      # rename user
```

### passwd
Set or change a user's password.

```bash
passwd username
```

### userdel
Delete a user.

```bash
userdel username          # delete user, keep home directory
userdel -r username       # delete user and home directory
```

## Groups

Groups control shared access to files and resources.

```bash
groupadd groupname              # create a group
groupdel groupname              # delete a group
groups username                 # list groups a user belongs to
id username                     # show UID, GID, and group memberships
```

## sudoers

The `/etc/sudoers` file controls who can use `sudo` and what they can run.

### visudo
Always edit sudoers with `visudo`, never directly with a text editor:

```bash
sudo visudo
```

`visudo` locks the file during editing and validates syntax before saving. A syntax error in sudoers can lock every user out of sudo — `visudo` prevents that.

### sudoers syntax

```
username ALL=(ALL:ALL) ALL      # full sudo access
username ALL=(ALL) NOPASSWD: /usr/bin/systemctl    # specific command, no password prompt
```

## /etc/passwd and /etc/shadow

`/etc/passwd` — stores user account info (username, UID, GID, home dir, shell). World-readable, does not store passwords.

`/etc/shadow` — stores hashed passwords. Root readable only.

```bash
cat /etc/passwd     # view user accounts
sudo cat /etc/shadow    # view password hashes (root only)
```

## Security Relevance

- Audit `/etc/passwd` for unexpected users on a potentially compromised system
- Users with UID 0 other than root are a red flag — UID 0 means root-level access regardless of username
- Check `/etc/sudoers` and `/etc/sudoers.d/` for unexpected privilege grants
- Principle of least privilege — users should only have access to what they need
