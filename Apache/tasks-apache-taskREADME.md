# Apache Virtual Host Setup for ilkin.com

## Overview
This project demonstrates how to set up a basic Apache HTTP server virtual host for the domain `ilkin.com`. It includes configuration for serving the main site and two subpaths (`/first` and `/second`) with distinct content directories using `Alias` directives. SELinux contexts and firewall settings are also configured.

## Project Structure
```
/ilkin.com/
├── index.html        # Main page content  
├── 1page/
│   └── index.html    # Content served at /first  
└── 2page/
    └── index.html    # Content served at /second
```

## Step-by-step Setup Instructions

### 1. Install Apache HTTP Server
```bash
yum install httpd -y
```

### 2. Create Virtual Host Configuration
Create a file `/etc/httpd/conf.d/ilkin.conf` with the following content:
```apache
<VirtualHost *:80>
    ServerName ilkin.com
    ServerAdmin ilkin@ilkin.com
    DocumentRoot /ilkin.com

    Alias /first    /ilkin.com/1page
    Alias /second   /ilkin.com/2page
</VirtualHost>
```

### 3. Configure Directory Permissions
Add the following to `/etc/httpd/conf/httpd.conf`:
```apache
<Directory /ilkin.com>
  Require all granted
  AllowOverride None
</Directory>
```

### 4. Create Content Directories and Files
```bash
mkdir -p /ilkin.com/1page /ilkin.com/2page
echo "Its MAIN PAGE!" > /ilkin.com/index.html
echo "Its 1ST PAGE!" > /ilkin.com/1page/index.html
echo "Its 2ST PAGE!" > /ilkin.com/2page/index.html
```

### 5. Configure SELinux Contexts
```bash
semanage fcontext -a -t httpd_sys_content_t "/ilkin.com(/.*)?"
restorecon -R -v /ilkin.com/
```

### 6. Start and Enable Apache Service
```bash
systemctl start httpd
systemctl enable httpd
```

### 7. Configure Firewall to Allow HTTP Traffic
```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
```

### 8. Test the Setup
Visit the following URLs in your browser:
- `http://ilkin.com/` — should display "Its MAIN PAGE!"
- `http://ilkin.com/first` — should display "Its 1ST PAGE!"
- `http://ilkin.com/second` — should display "Its 2ST PAGE!"

## Troubleshooting
- Check Apache status:
  ```bash
  systemctl status httpd
  ```
- Review Apache error logs:
  ```bash
  tail -f /var/log/httpd/error_log
  ```
- Verify SELinux status:
  ```bash
  sestatus
  ```
- Confirm firewall rules:
  ```bash
  firewall-cmd --list-all
  ```

## Notes
- Make sure DNS or `/etc/hosts` file resolves `ilkin.com` to your server's IP address for testing.
- This setup assumes a CentOS/RHEL environment with SELinux enabled.
