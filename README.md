# Linux Lab Notes

My notes from the Linux Essentials course at LLLC (Luxembourg Lifelong Learning Centre), the first step of the Linux Infrastructure Specialist diploma.

Each note covers what the lab was about, the key commands, and what I took away from it. These are my own summaries, not the course material.

## Labs

| # | Lab | Topics |
|---|-----|--------|
| 1 | [Linux distributions](01-linux-distros.md) | 8 VMs across 5 distros, package managers, SSH |
| 2 | [DHCP server with Kea](02-dhcp-server-kea.md) | netplan, static IP, Kea DHCPv4, leases |
| 3 | [Foreman](03-foreman.md) | Hostnames, APT repositories, Foreman + OpenVox |
| 4 | [Monitoring with Beszel](04-beszel-monitoring.md) | Docker, persistent volumes *(coming soon)* |
| 5 | [Company web service](05-nginx-web-service.md) | Nginx, fixing SSH, access logs |

## Lab setup

- Classroom: VMware ESXi, one isolated port group per student
- At home: VirtualBox, testing before I buy any hardware
- SSH client: MobaXterm

## Feedback welcome

I'm still learning. If you spot a mistake in a command or an explanation, please open an issue or tell me.
