# NGINX Reverse Proxy + Load Balancing (Round Robin)

This simple project demonstrates how to configure NGINX as a reverse proxy with round-robin load balancing between two backend servers running on the same machine.

## 🧱 Project Structure

```
/usr/share/nginx/html/backend/
├── backend1/
│   └── index.html ("it is my first server")
├── backend2/
    └── index.html ("it is my second server")

/etc/nginx/conf.d/
├── serven.conf   --> backend servers on ports 8080 and 8081
├── proxy.conf    --> reverse proxy with load balancing
```

---

## ⚙️ Setup Steps

### 1. Install and start NGINX
```bash
sudo yum install nginx -y
sudo systemctl enable --now nginx
```

### 2. Create backend directories and content
```bash
mkdir -p /usr/share/nginx/html/backend/backend1
echo "it is my first server" > /usr/share/nginx/html/backend/backend1/index.html

mkdir -p /usr/share/nginx/html/backend/backend2
echo "it is my second server" > /usr/share/nginx/html/backend/backend2/index.html
```

### 3. Create backend server config - `/etc/nginx/conf.d/serven.conf`
```nginx
server {
    listen 8080;
    server_name domain1;

    location / {
        root /usr/share/nginx/html/backend/backend1;
        index index.html;
    }
}

server {
    listen 8081;
    server_name domain2;

    location / {
        root /usr/share/nginx/html/backend/backend2;
        index index.html;
    }
}
```

### 4. Create proxy config - `/etc/nginx/conf.d/proxy.conf`
```nginx
upstream backend {
    server 127.0.0.1:8080;
    server 127.0.0.1:8081;
}

server {
    listen 80;
    server_name _;

    location / {
        proxy_set_header Host $host;
        proxy_pass http://backend;
    }
}
```

### 5. Disable default server block (optional but recommended)
Edit `/etc/nginx/nginx.conf` and comment out the default `server {}` block:
```nginx
# server {
#     listen       80;
#     ...
# }
```

### 6. Restart NGINX
```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## ✅ Test It
Run multiple curl commands to test round-robin load balancing:

```bash
curl http://127.0.0.1/
# Output: it is my first server

curl http://127.0.0.1/
# Output: it is my second server
```

---

## 📌 Notes
- Make sure SELinux is set to permissive or allow NGINX to access the custom directory:
```bash
setenforce 0
```

- Add `127.0.0.1 proxy` to `/etc/hosts` if testing with domain name.

---

## ✅ Result

You now have a basic working NGINX reverse proxy setup with round-robin load balancing. This setup will be the base for later steps such as setting up **Keepalived + Virtual IP** for HA (High Availability).

---
