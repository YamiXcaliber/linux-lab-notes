# Lab 5: Company web service with Nginx

**Scenario:** publish an internal IT support page for a company from a new Ubuntu Server VM, then prove a workstation can reach it.

I did this lab twice: once at home in VirtualBox (bridged adapter, Wi-Fi) and once at school (Ethernet). Each time the server got a different DHCP address.

## Step 10 first: fix SSH

The supplied server image had a broken SSH service, so this came first.

```bash
sudo systemctl status ssh     # was: inactive (dead), disabled
sudo /usr/sbin/sshd -t        # no output = config is valid
sudo systemctl enable --now ssh
sudo systemctl status ssh     # now: active (running), enabled
```

## 1. Find the server's address

```bash
whoami
hostname
ip -br -4 addr
hostname -I
```

## 2. Install Nginx

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl enable --now nginx
systemctl is-enabled nginx
systemctl status nginx
```

## 3. Publish the page

```bash
sudo tee /var/www/html/index.html > /dev/null <<'EOF'
<!doctype html>
<html lang="en">
<head><meta charset="utf-8"><title>IT Service Desk</title></head>
<body><h1>How can we help you today?</h1></body>
</html>
EOF
```

Open `http://<server-IP>` from the workstation.

## 4. Find the request in the logs

```bash
sudo tail -n 20 /var/log/nginx/access.log
```

The workstation's IP shows up in the log next to each request. That's the proof it worked.

## What I learned

- Read the whole lab before starting. Here, the last step had to be done first.
- `sshd -t` tests the SSH config without restarting anything.
- "Running" and "enabled" are two separate checks: one is now, the other is after a reboot.
- The access log is the server-side proof that a client really connected.
- A full clone in VirtualBox needs a new MAC address, or two VMs fight over the same DHCP lease.
