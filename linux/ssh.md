# SSH

## Overview

SSH (Secure Shell) allows secure remote command execution over an encrypted connection. It uses asymmetric encryption to establish the connection and authenticate users.

**SSH** is the client. **SSHD** is the daemon running on the server that receives and handles incoming SSH connections.

## Key-Based Authentication

While SSH can use a password, it's ideal to us a key pair instead:

- **Private key** — stays on your machine, never shared
- **Public key** — given to the server, stored in `~/.ssh/authorized_keys`

### How it works
When you connect, the server sends a challenge. Your client signs it with your **private key**. The server verifies the signature with your **public key**. If it matches, you're authenticated. Nothing secret is ever transmitted over the network.

### Generating a key pair
```bash
ssh-keygen
```

Adding a **passphrase** to your private key means even if someone gets the file, they cannot use it without the passphrase.

### Copying your public key to a server
```bash
ssh-copy-id user@server
```

## Connecting

```bash
ssh user@hostname
ssh user@hostname -p 2222          # specify port
ssh -i ~/.ssh/keyfile user@host    # specify key file
```

## SSH Config (~/.ssh/config)

Shortcuts for common connections:

```
Host myserver
    HostName 192.168.0.8
    User daxkharris
    IdentityFile ~/.ssh/my-key
    Port 22
```

Now `ssh myserver` works instead of typing the full command.

## Known Hosts

First time connecting to a server, SSH asks you to verify its fingerprint:

```
The authenticity of host '192.168.0.8' can't be established.
Are you sure you want to continue connecting? (yes/no)
```

This protects against man-in-the-middle attacks. The fingerprint is stored in `~/.ssh/known_hosts`. If it changes unexpectedly on a future connection, SSH will warn you — this could indicate an attack.

Most people hit yes without verifying. In a high-security context, the fingerprint should be verified out-of-band before accepting.

## Hardening (sshd_config)

Config file: `/etc/ssh/sshd_config`

```bash
PasswordAuthentication no      # keys only, no passwords
PermitRootLogin no             # root cannot SSH in directly
Port 2222                      # move off default port 22 (reduces automated scan noise)
AllowUsers daxkharris          # whitelist specific users
```

After editing, restart the daemon:
```bash
systemctl restart sshd
```

## fail2ban

Monitors logs for repeated failed authentication attempts and automatically bans the offending IP. Pairs well with key-only auth as an additional layer — even if someone tries to brute force, they get locked out after a configurable number of attempts.

## Security Principle

Disable SSH entirely on machines that don't need it. A service that isn't running cannot be exploited. If SSH is exposed to the internet, key-only auth and fail2ban are the minimum baseline.
