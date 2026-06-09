# Filesystem

## Structure

Windows uses a drive-letter hierarchy rather than a single unified tree. Each volume gets a letter (`C:`, `D:`, etc.) and has its own root. The primary filesystem for modern Windows is **NTFS**.

## Key Directories

| Directory | Purpose |
|-----------|---------|
| `C:\Windows` | Core OS files and system binaries |
| `C:\Windows\System32` | Primary system binaries and DLLs (64-bit on 64-bit Windows) |
| `C:\Windows\SysWOW64` | 32-bit binaries on 64-bit Windows |
| `C:\Program Files` | Installed 64-bit applications |
| `C:\Program Files (x86)` | Installed 32-bit applications on 64-bit Windows |
| `C:\Users` | User home directories (`C:\Users\username`) |
| `C:\Users\username\AppData` | Per-user app data — Local, Roaming, LocalLow |
| `C:\ProgramData` | Machine-wide app data, shared across users |
| `C:\Temp` / `%TEMP%` | Temporary files |
| `C:\Boot` | Boot loader files |

## NTFS Features

### Alternate Data Streams (ADS)

> Source: TryHackMe SOC Analyst Certificate — Windows module

NTFS allows additional named data streams to be attached to a file at the **filesystem level**, without modifying the file itself.

Every file has a default unnamed stream. ADS lets extra named streams be stapled alongside it:

```
file.txt          ← default stream (visible)
file.txt:hidden   ← alternate data stream (hidden)
```

The file's visible size only reflects the default stream. ADS data is invisible to normal directory listings and most tools.

**Key properties:**
- Stripped when copying to FAT32, exFAT, or other non-NTFS filesystems
- The file itself is untouched — ADS exists at the filesystem level only
- Persists on NTFS through normal copy/move operations

**Legitimate use:** Windows attaches a `Zone.Identifier` stream to files downloaded from the internet. This is what triggers the SmartScreen "are you sure?" prompt. This is your baseline for *normal* ADS activity.

**Malicious use:** Attackers hide executable payloads in ADS to evade detection. Because the file appears normal and unchanged, signature-based tools may miss it entirely. Classic **defense evasion** technique.

**Detection:**
```cmd
dir /r                        # list ADS on files in current directory
dir /r C:\suspicious\path     # target a specific path
streams.exe -s C:\path        # Sysinternals — more detailed output
```

**Quick reference:**

| Action | Command |
|--------|---------|
| Write to ADS | `echo data > file.txt:streamname` |
| Read from ADS | `more < file.txt:streamname` |
| List ADS (built-in) | `dir /r file.txt` |
| List ADS (Sysinternals) | `streams.exe file.txt` |

## Security Relevant Directories

### C:\Windows\System32
Contains critical system binaries. A common attacker technique is **DLL hijacking** — dropping a malicious DLL with the same name as a legitimate one in a location that gets searched first. Know the expected contents; unexpected binaries here are a red flag.

### C:\Users\username\AppData\Roaming
Roaming profile data that follows the user across machines in a domain. A common attacker persistence location — malicious executables and scheduled tasks are frequently dropped here because it is user-writable and often overlooked.

### C:\Temp and %TEMP%
User-writable temp directories. Same concern as `/tmp` on Linux — common staging ground for malware. Monitor for unexpected executables.

### C:\ProgramData
Machine-wide, writable by applications but not standard users. Another common persistence and payload staging location. Audit if a system is suspected compromised.
