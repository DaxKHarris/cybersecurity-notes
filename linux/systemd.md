# Systemd

## Overview

Systemd is the init system and service manager used by most modern Linux distributions. It is responsible for booting the system, managing services, handling logs, and more.

It is controversial in the Linux community for two reasons:
- It does not store logs in plain text files — it uses a binary journal, which some see as going against Linux's philosophy of transparency and user control
- It does a lot more than a traditional init system. Where something like GRUB just handles booting, systemd handles boot, service management, logging, timers, and more

## Boot Targets

Systemd replaced traditional runlevels with **targets**.

| Target | Equivalent Runlevel | Purpose |
|--------|--------------------|---------| 
| `poweroff.target` | 0 | Shut down |
| `rescue.target` | 1 | Single user / recovery |
| `multi-user.target` | 3 | Multi-user, no GUI |
| `graphical.target` | 5 | Multi-user with GUI |
| `reboot.target` | 6 | Reboot |

## systemctl

The primary command for managing services.

```bash
systemctl status jellyfin.service     # check status
systemctl start jellyfin.service      # start
systemctl stop jellyfin.service       # stop
systemctl restart jellyfin.service    # restart
systemctl enable jellyfin.service     # start on boot
systemctl disable jellyfin.service    # don't start on boot
systemctl daemon-reload               # reload after editing unit files
```

`enable` hooks the service into the boot target — it does not start it immediately. Use `enable --now` to do both at once.

## journalctl

Systemd's logging tool, replacing traditional syslog in many distros.

```bash
journalctl -u jellyfin.service              # logs for a specific service
journalctl -u jellyfin.service -f           # follow in real time
journalctl -u jellyfin.service -n 20        # last 20 lines
journalctl -u jellyfin.service --since "1 hour ago"   # time filter
journalctl -p err                           # errors only
```

## Unit Files

Service definitions live in:
- `/etc/systemd/system/` — user-defined, takes priority
- `/lib/systemd/system/` — package-installed defaults

Basic structure:

```ini
[Unit]
Description=My Script
After=network.target

[Service]
ExecStart=/home/user/scripts/myscript.sh
User=username
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

`WantedBy=multi-user.target` is what `systemctl enable` hooks into.

## Systemd Timers

Systemd replaced cron with timers. A `.timer` unit paired with a `.service` unit runs tasks on a schedule.

## Security Relevance

Attackers who gain access to a Linux system commonly drop malicious `.service` files in `/etc/systemd/system/` to persist across reboots. Auditing service files in that directory is an important step when investigating a potentially compromised host.
