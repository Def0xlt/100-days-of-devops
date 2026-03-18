# Day 15: Setup SSL for Nginx

## Task Description
Install and configure Nginx web server on App Server 2 with SSL/TLS encryption. The self-signed certificate and key files are already provided in `/tmp` directory. Move these certificates to appropriate secure locations, configure Nginx to use SSL on port 443, and create a simple welcome page.

## Solution Commands

### 1. Check the provided certificate files:
```bash
ls -l /tmp/*.crt /tmp/*.key
```

### 2. Install Nginx:
```bash
sudo yum update -y
sudo yum install -y nginx
```

### 3. Move SSL certificates to secure locations:
```bash
# Move certificate to /etc/ssl/certs/
sudo mv /tmp/nautilus.crt /etc/ssl/certs/

# Move private key to /etc/ssl/private/
sudo mkdir -p /etc/ssl/private
sudo mv /tmp/nautilus.key /etc/ssl/private/
```

### 4. Set appropriate permissions on SSL files:
```bash
# Certificate file should be readable
sudo chmod 644 /etc/ssl/certs/nautilus.crt

# Private key must be restricted
sudo chmod 600 /etc/ssl/private/nautilus.key

# Set correct ownership
sudo chown root:root /etc/ssl/certs/nautilus.crt
sudo chown root:root /etc/ssl/private/nautilus.key
```

### 5. Create welcome page:
```bash
# Create index.html with "Welcome!" message
echo "Welcome!" | sudo tee /usr/share/nginx/html/index.html
```

### 6. Configure Nginx for SSL:
```bash
# Backup original configuration
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak

# Edit Nginx configuration
sudo vi /etc/nginx/nginx.conf
```

Add or modify the server block inside the `http` section:

```nginx
server {
    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name _;

    ssl_certificate /etc/ssl/certs/nautilus.crt;
    ssl_certificate_key /etc/ssl/private/nautilus.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

**Vi editor commands:**
- Press `i` to enter insert mode
- Add/modify the server configuration
- Press `Esc` to exit insert mode
- Type `:wq` and press Enter to save and quit

### 7. Test Nginx configuration:
```bash
sudo nginx -t
```

Expected output: `nginx: configuration file /etc/nginx/nginx.conf test is successful`

### 8. Configure SELinux context (if SELinux is enabled):
```bash
# Check SELinux status
getenforce

# Restore SELinux context for SSL files
sudo restorecon -Rv /etc/ssl/certs/
sudo restorecon -Rv /etc/ssl/private/
```

### 9. Configure firewall to allow HTTP/HTTPS traffic:
```bash
# For firewalld
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload

# Verify firewall rules
sudo firewall-cmd --list-all
```

### 10. Start and enable Nginx:
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
```

### 11. Verify SSL configuration:
```bash
# Check if Nginx is listening on ports 80 and 443
sudo netstat -tuln | grep -E '80|443'
sudo ss -tuln | grep -E '80|443'

# Test HTTPS connection locally (bypass cert validation)
curl -k https://localhost/

# Test with headers to verify SSL handshake
curl -Ik https://localhost/

# Test from jump host (replace with actual IP)
curl -k https://<app-server-ip>/
curl -Ik https://<app-server-ip>/
```

Expected output: Should display "Welcome!" and HTTP 200 OK status

### 12. View detailed certificate information:
```bash
openssl x509 -in /etc/ssl/certs/nautilus.crt -text -noout
```

## Troubleshooting Steps

1. **Check Nginx status** - Is the service active and running?
2. **Verify certificate files** - Do they exist in correct locations?
3. **Check permissions** - Private key should be 600, certificate 644
4. **Test configuration** - Use `nginx -t` before restarting
5. **Review logs** - Check `/var/log/nginx/error.log`
6. **Firewall rules** - Ensure ports 80 and 443 are open
7. **SELinux context** - Verify with `ls -Z /etc/ssl/certs/` and `ls -Z /etc/ssl/private/`
8. **Port conflicts** - Check if another service is using port 443

## Success Indicators
- Nginx service status shows "Active: active (running)"
- Ports 80 and 443 show LISTEN status
- `curl -k https://localhost/` returns "Welcome!"
- `curl -Ik https://localhost/` shows HTTP 200 OK
- No errors in `nginx -t` output
- SSL certificate loads without errors (self-signed warning is expected)

## Common Obstacles
- **Permission denied errors:** Private key needs 600 permissions
- **Certificate not found:** Verify paths in nginx.conf match actual file locations
- **SELinux blocking:** Use `restorecon` to fix context or check audit logs
- **Firewall blocking:** Ports must be opened in firewall rules
- **Configuration syntax errors:** Missing semicolons or braces in nginx.conf
- **Port 443 already in use:** Check with `netstat` and stop conflicting service

## Notes
- Certificate and key files are pre-provided in `/tmp` directory
- Self-signed certificates will show browser warnings but work for testing
- The `-k` flag in curl bypasses certificate validation (needed for self-signed certs)
- Private key file must have restricted permissions (600) for security
- Certificate file permissions should be 644 (readable by all)
- HTTP traffic (port 80) should redirect to HTTPS (port 443)
- Use `journalctl -xe -u nginx` for detailed service logs
- Always test configuration with `nginx -t` before restarting the service
- SELinux context for certificates: `cert_t` for certs, `cert_t` for keys
- For production environments, use trusted CA-signed certificates

## Security Best Practices
- Keep private key files secure with 600 permissions
- Only root should own SSL certificate files
- Use strong cipher suites (TLSv1.2 and TLSv1.3)
- Disable older SSL/TLS protocols (SSLv2, SSLv3, TLSv1.0, TLSv1.1)
- Regular certificate renewal and monitoring
- Enable HSTS headers for enhanced security
- Monitor access logs for suspicious activity
