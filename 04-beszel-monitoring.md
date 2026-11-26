# Lab 4: Monitoring with Beszel

**Scenario:** a company runs Ubuntu, Debian and Rocky Linux servers, and admins check each one by hand over SSH. The task is to deploy a central, lightweight monitoring dashboard with Beszel.

**Phase 1:** deploy the Beszel Hub on Ubuntu Server with Docker.
**Phase 2:** install Beszel agents on Ubuntu, Debian and Rocky Linux.

## 1. Identify the server

```bash
hostnamectl
ip -br addr      # the lab uses DHCP, so note the current address
ip route
```

## 2. Install Docker from the official repository

```bash
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
# add /etc/apt/sources.list.d/docker.sources (Docker's official repo)
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable --now docker
```

Check it works:

```bash
sudo docker --version
sudo docker compose version
sudo docker run --rm hello-world
```

## 3. Deploy the hub with persistent storage

```bash
sudo docker volume create beszel_data
# run the Beszel hub container with the volume mounted and a restart policy
sudo docker ps
```

Then open the dashboard from a browser on the server's IP and create the admin account.

## 4. Prove it survives a reboot

```bash
sudo reboot
# after reboot:
sudo docker ps    # hub container should be running again
```

## What I learned

- Install Docker from Docker's own repository, not an old distro package.
- A container is disposable. Data lives in a **volume**, so it survives updates and restarts.
- A restart policy plus `systemctl enable docker` is what makes the service come back after a reboot.
- On DHCP networks, always check the current IP before telling someone the URL.
- Centralised monitoring answers "is everything up?" without logging into each server.
