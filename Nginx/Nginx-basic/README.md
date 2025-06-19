# Nginx Virtual Host Task: test.az with Multiple Locations

## 📝 Task Description

This task demonstrates how to configure a basic Nginx virtual host with multiple `location` blocks. The goal is to serve different content based on the requested URL path (`/`, `/first`, `/second`) using separate folders and proper redirects.

---

## 📁 Folder Structure

The website content is organized as follows:

```
/usr/share/nginx/html/test.az/
├── main/
│   └── index.html        → Main page
├── first1/
│   └── index.html        → First subpage
├── second2/
│   └── index.html        → Second subpage
```

---

## ⚙️ Configuration Overview

- `/` serves content from `main/index.html`
- `/first` redirects to `/first/`, which serves content from `first1/index.html`
- `/second` redirects to `/second/`, which serves content from `second2/index.html`
- Custom 404 page is displayed if path not found

---

## 📌 Step-by-Step Setup

### 1. Install and start Nginx

```bash
sudo yum install -y nginx
sudo systemctl enable --now nginx
```

---

### 2. Create directories and HTML files

```bash
sudo mkdir -p /usr/share/nginx/html/test.az/main
sudo mkdir -p /usr/share/nginx/html/test.az/first1
sudo mkdir -p /usr/share/nginx/html/test.az/second2

echo "Its MAIN PAGE!" | sudo tee /usr/share/nginx/html/test.az/main/index.html
echo "1ci sehife" | sudo tee /usr/share/nginx/html/test.az/first1/index.html
echo "2ci sehife" | sudo tee /usr/share/nginx/html/test.az/second2/index.html

echo "<h1>404 Not Found</h1><p>Sorry, the page you requested was not found.</p>" | sudo tee /usr/share/nginx/html/test.az/main/404.html
```

---

### 3. Nginx virtual host config

Create `/etc/nginx/conf.d/test.az.conf`:

```nginx
server {
    listen 80;
    server_name test.az;

    location / {
        root /usr/share/nginx/html/test.az/main;
        index index.html;
    }

    location = /first {
        return 301 http://test.az/first/;
    }

    location /first/ {
        alias /usr/share/nginx/html/test.az/first1/;
        index index.html;
    }

    location = /second {
        return 301 http://test.az/second/;
    }

    location /second/ {
        alias /usr/share/nginx/html/test.az/second2/;
        index index.html;
    }

    error_page 404 /404.html;
    location = /404.html {
        root /usr/share/nginx/html/test.az/main;
        internal;
    }
}
```

---

### 4. Test and reload Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🧪 Test URLs

```bash
curl http://test.az/          # Should return: Its MAIN PAGE!
curl http://test.az/first/    # Should return: 1ci sehife
curl http://test.az/second/   # Should return: 2ci sehife

curl -I http://test.az/first  # Should return: HTTP 301 -> /first/
curl -I http://test.az/second # Should return: HTTP 301 -> /second/
```

---

## 🖥️ /etc/hosts Setup

To access `test.az` locally, add the following to your `hosts` file:

### Linux `/etc/hosts`

```
192.168.x.x test.az
```

### Windows `C:\Windows\System32\drivers\etc\hosts`

```
192.168.x.x test.az
```

> Replace `192.168.x.x` with your Nginx server's IP address.

---

## 📚 Extra Notes

- The `return 301` is used to redirect `/first` to `/first/` (same for `/second`) which ensures correct path resolution.
- The `alias` directive points directly to folders, unlike `root`.
- The `internal` keyword ensures `/404.html` cannot be accessed directly.

---

## ✅ Result

This task demonstrates a clean way to manage multiple paths in a single domain using Nginx. It's a simple but useful setup for beginners learning Nginx virtual hosting and URL path handling.
