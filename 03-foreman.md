# Lab 3: Foreman

**Goal:** install Foreman (a lifecycle and configuration management server) on Debian 13, then prepare it to manage other Linux VMs.

Also in this lab: installing Rocky Linux and openSUSE Leap (server and desktop), and importing VMs from OVA files.

## 1. Check the server first

Foreman needs resources: 4 vCPU, 8 GB RAM, 80 GB disk.

```bash
cat /etc/os-release
nproc
free -h
lsblk
df -h /
ip -br address
ip route
```

Note: growing a virtual disk in ESXi doesn't grow the Linux filesystem. That's a separate step.

## 2. Set a proper hostname

Foreman needs a fully qualified hostname that resolves to the server's real IP.

```bash
hostnamectl set-hostname foreman.lab.test
cp -a /etc/hosts /etc/hosts.before-foreman   # backup
nano /etc/hosts                              # <server-IP> foreman.lab.test foreman
hostname -f                                  # must return foreman.lab.test
getent ahostsv4 foreman.lab.test             # must return the server IP
```

## 3. Add the repositories

```bash
apt install -y ca-certificates wget gnupg
# OpenVox 8 (open-source Puppet) repository package for Debian 13
# Foreman signing key + Foreman 5.0 core and plugin repositories
apt update
apt-cache policy foreman-installer
```

## 4. Install and verify

```bash
apt install -y foreman-installer
foreman-installer                                   # note the URL, admin user and password it prints
systemctl is-active foreman foreman-proxy puppetserver
systemctl --failed --no-pager
/opt/puppetlabs/bin/puppet agent --test
```

Then log in at `https://<server-IP>` and change the admin password straight away.

## 5. Remote execution

```bash
foreman-installer \
  --enable-foreman-plugin-remote-execution \
  --enable-foreman-proxy-plugin-remote-execution-script
```

## What I learned

- Hostname and name resolution must be right **before** installing. Many installers depend on it.
- Make the server's IP persistent before installing, so the hostname always points to the right address.
- Adding a third-party repository = trusting its signing key, then adding the source list.
- `systemctl --failed` is a quick health check after any big install.
- An installer's log file (`/var/log/foreman-installer/`) is the first place to look when something fails.
