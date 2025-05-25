# Apache HTTPD Task: Reverse Proxy and VirtualHost Setup

This guide explains how to configure Apache HTTPD with two VirtualHosts and a reverse proxy on port 8080.

---

## 📦 1. Install Apache HTTPD

```bash
sudo yum install httpd -y
sudo systemctl enable --now httpd
```

---

## 📁 2. Create Content for Virtual Hosts

```bash
cd /var/www
mkdir domain1 domain2
echo "Its domain1" > domain1/index.html
echo "Its domain2" > domain2/index.html
```

---

## ⚙️ 3. Configure Virtual Hosts in a Single File

Create a single configuration file:

```bash
cd /etc/httpd/conf.d
vim domain.conf
```

Add the following content:

```apache
<VirtualHost *:80>
    ServerName domain1
    ServerAdmin murad@domain1
    DocumentRoot /var/www/domain1
</VirtualHost>

<VirtualHost *:80>
    ServerName domain2
    ServerAdmin murad@domain2
    DocumentRoot /var/www/domain2
</VirtualHost>
```

---

## 🧪 4. Test Virtual Hosts Without /etc/hosts

Use curl with custom Host headers:

```bash
curl -H "Host: domain1" http://127.0.0.1
curl -H "Host: domain2" http://127.0.0.1
```

Expected output:
```
Its domain1
Its domain2
```

---

## 🔄 5. Enable Reverse Proxy

Ensure mod_proxy and mod_proxy_http are enabled:

```bash
sudo yum install mod_proxy mod_proxy_http -y
```

Create a reverse proxy configuration:

```bash
cd /etc/httpd/conf.d
vim proxy.conf
```

Add this content:

```apache
<VirtualHost *:80>
    ServerName proxytest
    ProxyRequests Off
    ProxyPreserveHost On

    ProxyPass /app http://localhost:8080/
    ProxyPassReverse /app http://localhost:8080/
</VirtualHost>
```

---

## ✅ 6. Check Configuration and Restart Apache

```bash
sudo apachectl configtest
sudo systemctl restart httpd
```

---

## 📌 Notes

- The `ServerName` in each VirtualHost should match the Host header you test with using curl.
- If you want to use a browser instead of curl, you need to define the domains in `/etc/hosts` or configure DNS.

