
# Task 3 — Keepalived + Virtual IP Setup with Nginx

## Goal
Set up two servers (e.g., 192.168.100.101 and 192.168.100.102) with Keepalived to provide high availability using a Virtual IP (VIP). Configure Nginx on both servers to serve as backend servers. If the master server goes down, the backup server automatically takes over the VIP and serves traffic seamlessly.

---

## Setup Steps

### 1. Install necessary packages
```bash
sudo yum install nginx -y
sudo systemctl enable --now nginx
sudo yum install keepalived -y
sudo systemctl enable --now keepalived
```

### 2. Configure Keepalived on First Server (Master)
Edit `/etc/keepalived/keepalived.conf`:
```bash
vrrp_instance VI_1 {
    state MASTER
    interface ens160
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.100.200
    }
}
```

### 3. Configure Keepalived on Second Server (Backup)
Edit `/etc/keepalived/keepalived.conf`:
```bash
vrrp_instance VI_1 {
    state BACKUP
    interface ens160
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.100.200
    }
}
```

### 4. Create simple HTML files to identify each server

On First Server:
```bash
cd /usr/share/nginx/html/
echo "This is First Server" > index.html
```

On Second Server:
```bash
cd /usr/share/nginx/html/
echo "This is Second Server" > index.html
```

### 5. Configure Nginx for each server

On First Server, create `/etc/nginx/conf.d/firstserver.conf` with appropriate config (optional, basic default will serve index.html).

On Second Server, create `/etc/nginx/conf.d/secondserver.conf` similarly.

### 6. Test the Setup

- Access the VIP `192.168.100.200` in browser or via curl.
- Stop (shutdown) the master server and verify the backup server takes over the VIP and serves the page.

---

## Notes
- `virtual_router_id` must be the same on both servers.
- `priority` determines which server is master (higher priority wins).
- `interface` must be the network interface connected to the VIP subnet.
- `auth_pass` should be the same for authentication between nodes.
- Keep firewalls and SELinux configured properly or disabled to allow VRRP traffic.

---

## Troubleshooting
- Check `journalctl -u keepalived -f` for Keepalived logs.
- Verify VIP assigned using `ip addr` command.
- Ensure network interfaces and IPs are correct.
- If VIP doesn't failover, check priorities and interface names.

---

Task Complete! 🎉
