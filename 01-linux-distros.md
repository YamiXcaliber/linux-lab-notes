# Lab 1: Linux distributions

**Goal:** install eight VMs across five distributions, then compare how each one is identified, updated and reached over SSH.

| VM | Distribution | Desktop |
|----|--------------|---------|
| 1–2 | Ubuntu LTS | Server / Desktop |
| 3–4 | Debian 13 | Server / GNOME |
| 5–6 | Rocky Linux | Server / Desktop |
| 7 | openSUSE Leap | Desktop |
| 8 | AlmaLinux | Server |

## Identify the machine

```bash
cat /etc/os-release   # distribution and version
hostname              # machine name
hostname -I           # IP addresses (can be several)
uname -r              # running kernel
```

## Package managers

| Task | Ubuntu / Debian | Rocky / AlmaLinux | openSUSE |
|------|-----------------|-------------------|----------|
| Refresh | `sudo apt update` | `sudo dnf check-update` | `sudo zypper refresh` |
| Upgrade | `sudo apt upgrade` | `sudo dnf upgrade --refresh` | `sudo zypper update` |
| Search | `apt search tree` | `dnf search tree` | `zypper search tree` |
| Info | `apt show tree` | `dnf info tree` | `zypper info tree` |
| Installed? | `dpkg -s tree` | `rpm -q tree` | `rpm -q tree` |

## Enable SSH

```bash
# Ubuntu / Debian (service is called ssh)
sudo apt install openssh-server
sudo systemctl enable --now ssh

# Rocky / AlmaLinux / openSUSE (service is called sshd)
sudo dnf install openssh-server      # or: sudo zypper install openssh
sudo systemctl enable --now sshd
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

## What I learned

- Two big package families: APT (`.deb`) and DNF/Zypper (`.rpm`). The commands differ, the ideas are the same.
- The SSH service name differs: `ssh` on Debian/Ubuntu, `sshd` on the RHEL family.
- SSH needs two things: the service running **and** the firewall allowing it.
- `dnf check-update` returns exit code 100 when updates exist. That's normal, not an error.
- In ESXi, when the exact OS version isn't in the list, pick the nearest lower one (e.g. Debian 12 64-bit for Debian 13). It only sets virtual hardware defaults.
- A kernel update needs a reboot before `uname -r` shows the new version.
