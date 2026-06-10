# Package Management

Different Linux distributions use different package managers. The commands differ but the concepts are the same — install, remove, update, and search for software.

## apt (Debian / Ubuntu)

```bash
apt update                      # refresh package index
apt upgrade                     # upgrade installed packages
apt install packagename         # install a package
apt remove packagename          # remove a package
apt autoremove                  # remove unused dependencies
apt search packagename          # search for a package
apt show packagename            # show package details
```

Always run `apt update` before `apt upgrade` or installing packages.

## pacman (Arch)

```bash
pacman -Syu                     # sync and full system upgrade
pacman -S packagename           # install a package
pacman -R packagename           # remove a package
pacman -Rs packagename          # remove with unused dependencies
pacman -Ss packagename          # search for a package
pacman -Qi packagename          # show installed package info
```

`sudo pacman -Syu` is the standard way to keep an Arch system up to date.

## dnf (Fedora / RHEL)

```bash
dnf update                      # update all packages
dnf install packagename         # install a package
dnf remove packagename          # remove a package
dnf search packagename          # search for a package
dnf info packagename            # show package details
```

## Security Relevance

- Keep packages updated — the majority of exploits target known vulnerabilities with available patches
- Only install packages from trusted repositories
- Remove packages that are no longer needed — reduces attack surface
- `apt list --installed` / `pacman -Q` / `dnf list installed` — audit installed software on a potentially compromised system for unexpected packages
