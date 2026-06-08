# Permissions

## The Three Permission Groups

Every file and directory has three groups:

- **Owner** — the user who owns the file
- **Group** — a group of users assigned to the file
- **Others** — everyone else

## The Three Permission Types

| Permission | Symbol | File | Directory |
|------------|--------|------|-----------|
| Read | `r` | View file contents | List directory contents |
| Write | `w` | Modify file contents | Create/delete files inside |
| Execute | `x` | Run as script/program | Enter the directory |

## Reading Permissions

`ls -l` shows permissions as a string:

```
-rwxr-xr--
```

Breaking it down:
- `-` — file type (`-` = file, `d` = directory, `l` = symlink)
- `rwx` — owner: read, write, execute
- `r-x` — group: read, execute
- `r--` — others: read only

## chmod

Modify permissions with `chmod`.

### Symbolic Mode

```bash
chmod u+x file        # add execute for owner
chmod g-w file        # remove write for group
chmod o=r file        # set others to read only
chmod a+x file        # add execute for all (a = all)
```

`u` = owner, `g` = group, `o` = others, `a` = all  
`+` = add, `-` = remove, `=` = set exactly

### Octal Mode

Each permission has a value:
- Read = `4`
- Write = `2`
- Execute = `1`

Add them together for each group. Three digits: owner, group, others.

```bash
chmod 755 file    # rwxr-xr-x
chmod 644 file    # rw-r--r--
chmod 600 file    # rw------- (owner only, common for SSH keys)
chmod 777 file    # rwxrwxrwx (avoid — everyone can write and execute)
```

`chmod 777` is a security risk — any user including an unprivileged attacker can modify and execute the file.

## chown

Change file ownership.

```bash
chown username file           # change owner
chown username:group file     # change owner and group
chown -R username /path       # recursive
```

**When to use:** if a script or file was created as root but should be owned by a normal user, `chown` transfers ownership so it runs with reduced privileges. This follows the principle of least privilege — nothing should run with more access than it needs.

## sudo vs root

- `sudo command` — run a single command as root, then return to normal user
- `su` — switch to another user (`su` alone switches to root)
- `su -` — switch user and load their full environment

**Never stay logged in as root.** Root has no guardrails — commands like `rm -rf /` will execute without complaint. `sudo` forces intentionality — you have to explicitly escalate for each privileged action.

`sudo` is also needed to `chmod` or `chown` files you don't own, such as system files owned by root.
