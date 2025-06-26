
# Task 2 — Nginx Virtual Hosts and Redirects

## Goal
Create two virtual hosts for two domains and redirect non-www domains to their www versions using 301 permanent redirects.

---

## Setup Overview

- Serve **www.firstsite.com** from `/usr/share/nginx/html/domen/domen1/index.html`
- Serve **www.secondsite.com** from `/usr/share/nginx/html/domen/domen2/index.html`
- Redirect **firstsite.com** to **www.firstsite.com**
- Redirect **secondsite.com** to **www.secondsite.com**

---

## Steps

### 1. Install Nginx
```bash
sudo yum install nginx -y
sudo systemctl enable --now nginx
```

### 2. Create website content
```bash
cd /usr/share/nginx/html/
mkdir -p domen/domen1 domen/domen2

echo "Welcome to www.firstsite.com" > domen/domen1/index.html
echo "Welcome to www.secondsite.com" > domen/domen2/index.html
```

### 3. Configure Nginx virtual hosts

Edit `/etc/nginx/conf.d/domen.conf` and add the following:

```nginx
server {
    listen 80;
    server_name www.firstsite.com;

    location / {
        root /usr/share/nginx/html/domen/domen1;
        index index.html;
    }
}

server {
    listen 80;
    server_name www.secondsite.com;

    location / {
        root /usr/share/nginx/html/domen/domen2;
        index index.html;
    }
}

server {
    listen 80;
    server_name firstsite.com;
    return 301 http://www.firstsite.com$request_uri;
}

server {
    listen 80;
    server_name secondsite.com;
    return 301 http://www.secondsite.com$request_uri;
}
```

### 4. Edit `/etc/hosts` for local testing
Add the following lines to your `/etc/hosts` file (Linux) or `C:\Windows\System32\drivers\etc\hosts` (Windows):

```
127.0.0.1 www.firstsite.com firstsite.com
127.0.0.1 www.secondsite.com secondsite.com
```

### 5. Test Nginx configuration and reload
```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## Testing

Use the following commands to test:

```bash
curl http://www.secondsite.com
# Output: Welcome to www.secondsite.com

curl http://secondsite.com
# Output: 301 Moved Permanently (redirect)

curl -L http://secondsite.com
# Output: Welcome to www.secondsite.com (after following redirect)
```

Open your browser and test:

- `http://secondsite.com` should redirect to `http://www.secondsite.com`
- `http://firstsite.com` should redirect to `http://www.firstsite.com`

---

## Notes

- `301` means permanent redirect.
- Browsers follow redirects automatically.
- Curl requires the `-L` flag to follow redirects.

---

## Task Complete 🎉
