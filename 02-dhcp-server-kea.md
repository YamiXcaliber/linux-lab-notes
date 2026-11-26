# Lab 2: DHCP server with Kea

**Goal:** turn an Ubuntu Server VM into a DHCP server for an isolated network, using ISC Kea DHCPv4.

The server has two network adapters:
- one for management and SSH (gets its own address by DHCP)
- one for the isolated lab network (static IP, serves DHCP)

## 1. Static IP with netplan

```bash
ip -br link show          # check the second adapter exists
cat /etc/netplan/*.yaml   # look before changing anything
```

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens34:            # management
      dhcp4: true
    ens35:            # lab network
      dhcp4: false
      addresses:
        - 10.10.10.13/24
```

```bash
sudo netplan generate   # check for errors
sudo netplan try        # applies, rolls back if you lose access
ip -br address
```

## 2. Install Kea

```bash
sudo apt update
sudo apt install -y kea-dhcp4-server
kea-dhcp4 -V
```

## 3. Configure the scope

Back up first: `sudo cp -a /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.backup`

```json
{
  "Dhcp4": {
    "interfaces-config": { "interfaces": [ "ens35" ] },
    "lease-database": {
      "type": "memfile",
      "persist": true,
      "name": "/var/lib/kea/kea-leases4.csv"
    },
    "valid-lifetime": 86400,
    "renew-timer": 43200,
    "rebind-timer": 75600,
    "subnet4": [
      {
        "id": 1,
        "subnet": "10.10.10.0/24",
        "pools": [ { "pool": "10.10.10.20 - 10.10.10.254" } ]
      }
    ]
  }
}
```

## 4. Test and start

```bash
sudo kea-dhcp4 -t /etc/kea/kea-dhcp4.conf   # validate before restarting
sudo systemctl restart kea-dhcp4-server
sudo systemctl enable kea-dhcp4-server
sudo cat /var/lib/kea/kea-leases4.csv        # see leases handed out
```

## What I learned

- Always look at the current config and make a backup before editing.
- `netplan try` is safer than `netplan apply` over SSH: it rolls back if you lose the connection.
- Kea's JSON config is strict. One missing comma or bracket and the service won't start, so validate with `-t` first.
- Lease timers: T1 (renew) at 50%, T2 (rebind) at 87.5% of the lease time.
- `enable` = start at boot, `start` = start now. `enable --now` does both.
- Bind DHCP to the lab interface only, so it never answers on the real network.
