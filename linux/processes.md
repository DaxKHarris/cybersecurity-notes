# Processes

## Viewing Processes

### ps
Snapshot of current processes.

```bash
ps aux
```

- `a` — show processes from all users
- `u` — user-readable format
- `x` — include processes not attached to a terminal

Key columns: `USER`, `PID`, `%CPU`, `%MEM`, `COMMAND`

### top / htop
Real-time process monitor. Similar to Windows Task Manager.

```bash
top       # built-in
htop      # improved version, more interactive (install separately)
```

### pgrep
Find a process PID by name.

```bash
pgrep jellyfin
```

## Killing Processes

```bash
kill PID          # send SIGTERM (graceful)
kill -15 PID      # same as above, explicit
kill -9 PID       # SIGKILL (force, no cleanup)
pkill processname # kill by name instead of PID
```

### Signals

| Signal | Number | Meaning |
|--------|--------|---------|
| SIGTERM | 15 | Polite shutdown — asks process to stop gracefully |
| SIGKILL | 9 | Force kill — kernel terminates immediately, no cleanup |
| SIGHUP | 1 | Reload config without restarting (common for services) |

Always try `SIGTERM` first. `SIGKILL` doesn't allow the process to clean up — can leave lock files or corrupt state.

## Security Relevance

Unexpected processes are a red flag on a potentially compromised system. Look for:

- Processes running from `/tmp`
- Processes owned by unexpected users
- Processes with unusual names or no readable command
- High resource usage with no clear cause

`ps aux` and `/proc/[pid]/` are among the first places to check during forensic investigation of a compromised host.
