# Day 19: Install and Configure Web Application

## Task Description
xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter. The development of these websites is still in-progress, but we want to get the servers ready. The task involves setting up Apache HTTP Server to serve multiple static websites on a custom port.

## Requirements
- Install `httpd` package and dependencies on app server 1
- Configure Apache to serve on port **8080**
- Deploy two website backups from the jump host: `/home/thor/ecommerce` and `/home/thor/games`
- Set up Apache so:
  - ecommerce is available at `http://localhost:8080/ecommerce/`
  - games is available at `http://localhost:8080/games/`
- Verify using `curl` on the app server

## Solution Commands

### 1. Install Apache (httpd):
```bash
# For RHEL/CentOS/Alma/Rocky based systems
sudo yum install -y httpd

# Or if using dnf
sudo dnf install -y httpd

# For Debian/Ubuntu based systems
sudo apt update
sudo apt install -y apache2
```

### 2. Verify Apache installation:
```bash
httpd -v
# or for Debian/Ubuntu
apache2 -v
```

Expected output shows Apache version information.

### 3. Configure Apache to listen on port 8080:

Edit the Apache configuration file to change the listening port:

```bash
# For RHEL/CentOS systems
sudo vi /etc/httpd/conf/httpd.conf
```

Find and update the Listen directive:
```conf
Listen 8080
```

### 4. Create or update VirtualHost configuration:

Create a new virtual host configuration file:

```bash
sudo vi /etc/httpd/conf.d/000-default.conf
```

Add the following configuration:
```apache
<VirtualHost *:8080>
    ServerName localhost
    DocumentRoot "/var/www/html"

    <Directory "/var/www/html">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

    Alias /ecommerce/ "/var/www/html/ecommerce/"
    Alias /games/ "/var/www/html/games/"

    <Directory "/var/www/html/ecommerce">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

    <Directory "/var/www/html/games">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

### 5. Enable and start Apache service:
```bash
sudo systemctl enable httpd
sudo systemctl start httpd
sudo systemctl status httpd
```

Expected output should show `Active: active (running)`

### 6. Configure firewall to allow port 8080:
```bash
# For firewalld (RHEL/CentOS)
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# For UFW (Debian/Ubuntu)
sudo ufw allow 8080/tcp
```

### 7. Copy website backups from jump host to app server:

**Option A: From the jump host (push to app server):**
```bash
scp -r /home/thor/ecommerce/ user@app_server_1:/tmp/
scp -r /home/thor/games/ user@app_server_1:/tmp/
```

**Option B: From the app server (pull from jump host):**
```bash
scp -r thor@jump_host:/home/thor/ecommerce /tmp/
scp -r thor@jump_host:/home/thor/games /tmp/
```

### 8. Move directories to Apache document root:
```bash
sudo mv /tmp/ecommerce /var/www/html/
sudo mv /tmp/games /var/www/html/
```

### 9. Set proper ownership and permissions:
```bash
# Set ownership to Apache user
sudo chown -R apache:apache /var/www/html/ecommerce
sudo chown -R apache:apache /var/www/html/games

# Set directory permissions (755)
sudo find /var/www/html/ecommerce -type d -exec chmod 755 {} +
sudo find /var/www/html/games -type d -exec chmod 755 {} +

# Set file permissions (644)
sudo find /var/www/html/ecommerce -type f -exec chmod 644 {} +
sudo find /var/www/html/games -type f -exec chmod 644 {} +
```

### 10. Configure SELinux contexts (if SELinux is enabled):
```bash
# Check if SELinux is enabled
getenforce

# If SELinux is enforcing, set proper contexts
sudo chcon -R -t httpd_sys_content_t /var/www/html/ecommerce
sudo chcon -R -t httpd_sys_content_t /var/www/html/games

# Make the context change persistent
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/html/ecommerce(/.*)?"
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/html/games(/.*)?"
sudo restorecon -Rv /var/www/html/ecommerce
sudo restorecon -Rv /var/www/html/games
```

### 11. Restart Apache to apply all changes:
```bash
sudo systemctl restart httpd
sudo systemctl status httpd
```

## Verification Steps

### Verify Apache is running and listening on port 8080:
```bash
sudo systemctl status httpd
sudo ss -tlnp | grep 8080
sudo netstat -tuln | grep 8080
```

Expected output should show Apache listening on port 8080.

### Verify directories exist with correct content:
```bash
ls -la /var/www/html/
ls -la /var/www/html/ecommerce/
ls -la /var/www/html/games/
```

### Test ecommerce site access:
```bash
curl -I http://localhost:8080/ecommerce/
curl http://localhost:8080/ecommerce/
```

Expected: HTTP/1.1 200 OK and HTML content displayed.

### Test games site access:
```bash
curl -I http://localhost:8080/games/
curl http://localhost:8080/games/
```

Expected: HTTP/1.1 200 OK and HTML content displayed.

### Verify from remote machine (if applicable):
```bash
curl -I http://app_server_1_ip:8080/ecommerce/
curl -I http://app_server_1_ip:8080/games/
```

## Troubleshooting Steps

1. **Check Apache status** - Is the service active and running?
   ```bash
   sudo systemctl status httpd
   sudo journalctl -u httpd -n 50
   ```

2. **Verify Apache is listening on port 8080**
   ```bash
   sudo ss -tlnp | grep 8080
   sudo netstat -tuln | grep 8080
   ```

3. **Check Apache error logs**
   ```bash
   sudo tail -f /var/log/httpd/error_log
   sudo tail -f /var/log/httpd/access_log
   ```

4. **Verify configuration syntax**
   ```bash
   sudo httpd -t
   # or
   sudo apachectl configtest
   ```
   Expected output: `Syntax OK`

5. **Check file and directory permissions**
   ```bash
   sudo ls -laR /var/www/html/ecommerce
   sudo ls -laR /var/www/html/games
   ```

6. **Verify SELinux contexts (if SELinux is enabled)**
   ```bash
   ls -laZ /var/www/html/ecommerce
   ls -laZ /var/www/html/games
   ```

7. **Check if another service is using port 8080**
   ```bash
   sudo lsof -i :8080
   sudo ss -tlnp | grep 8080
   ```

8. **Verify firewall rules**
   ```bash
   # For firewalld
   sudo firewall-cmd --list-all

   # For UFW
   sudo ufw status verbose
   ```

9. **Check if index files exist**
   ```bash
   ls -la /var/www/html/ecommerce/index.html
   ls -la /var/www/html/games/index.html
   ```

10. **Test with detailed curl output**
    ```bash
    curl -v http://localhost:8080/ecommerce/
    curl -v http://localhost:8080/games/
    ```

## Success Indicators
- Apache (httpd) service is installed and running
- Apache service status shows `Active: active (running)`
- Port 8080 shows LISTEN status with httpd process
- Directories `/var/www/html/ecommerce` and `/var/www/html/games` exist with proper content
- `curl http://localhost:8080/ecommerce/` returns HTTP 200 OK and HTML content
- `curl http://localhost:8080/games/` returns HTTP 200 OK and HTML content
- No errors in Apache error log related to the configuration
- Configuration syntax check passes with `Syntax OK`
- Files have correct ownership (apache:apache) and permissions (directories: 755, files: 644)

## Common Obstacles

### Port already in use:
If port 8080 is already in use by another service:
```bash
# Find what's using the port
sudo lsof -i :8080
sudo ss -tlnp | grep 8080

# Stop the conflicting service or choose a different port
```

### SELinux blocking access:
SELinux can prevent Apache from reading files:
```bash
# Check SELinux denials
sudo ausearch -m avc -ts recent

# Temporarily set SELinux to permissive for testing
sudo setenforce 0

# If it works, set proper contexts (see step 10 above)
# Re-enable SELinux
sudo setenforce 1
```

### Permission denied errors:
```bash
# Ensure Apache user can read the files
sudo chown -R apache:apache /var/www/html/ecommerce /var/www/html/games
sudo chmod -R 755 /var/www/html/ecommerce /var/www/html/games
```

### Missing index file (403 Forbidden):
```bash
# Check if index.html exists
ls /var/www/html/ecommerce/index.html
ls /var/www/html/games/index.html

# If missing, either create one or enable directory indexing
# Add to <Directory> block: Options +Indexes
```

### Configuration file errors:
```bash
# Always test configuration before restarting
sudo httpd -t

# View detailed error messages
sudo journalctl -xeu httpd
```

## Apache Commands Reference

### Service management:
```bash
# Start Apache
sudo systemctl start httpd

# Stop Apache
sudo systemctl stop httpd

# Restart Apache
sudo systemctl restart httpd

# Reload configuration without dropping connections
sudo systemctl reload httpd

# Enable Apache to start on boot
sudo systemctl enable httpd

# Check status
sudo systemctl status httpd
```

### Configuration testing:
```bash
# Test configuration syntax
sudo httpd -t
sudo apachectl configtest

# View loaded modules
httpd -M

# View compiled-in settings
httpd -V
```

### Log monitoring:
```bash
# Watch error log in real-time
sudo tail -f /var/log/httpd/error_log

# Watch access log in real-time
sudo tail -f /var/log/httpd/access_log

# View last 100 lines of error log
sudo tail -n 100 /var/log/httpd/error_log
```

## Notes
- Apache uses the `apache` user on RHEL/CentOS systems and `www-data` on Debian/Ubuntu
- The default document root is `/var/www/html/`
- Configuration files are located in `/etc/httpd/conf/` and `/etc/httpd/conf.d/`
- Always test configuration with `httpd -t` before restarting the service
- SELinux must allow Apache to read content with the `httpd_sys_content_t` context
- Port 8080 is a common alternative HTTP port (default is 80)
- The `Alias` directive maps URL paths to filesystem locations
- Ensure index files (index.html) exist in each directory for proper access

## Security Best Practices
- Keep Apache updated with security patches
- Disable directory listing in production (remove `Indexes` from Options)
- Use minimal required permissions (644 for files, 755 for directories)
- Configure proper firewall rules to restrict access
- Enable HTTPS with SSL/TLS certificates for production
- Disable unnecessary Apache modules
- Configure Apache to run as a non-privileged user
- Implement proper access controls using `.htaccess` or `<Directory>` blocks
- Regular review Apache access and error logs for suspicious activity
- Hide Apache version information (ServerTokens Prod, ServerSignature Off)

## Additional Configuration (Optional)

### Enable detailed error pages:
```apache
# Add to VirtualHost block
ErrorDocument 404 /custom_404.html
ErrorDocument 500 /custom_500.html
```

### Configure custom log format:
```apache
# Add to VirtualHost block
CustomLog /var/log/httpd/ecommerce_access.log combined
CustomLog /var/log/httpd/games_access.log combined
```

### Set up basic authentication (optional):
```bash
# Create password file
sudo htpasswd -c /etc/httpd/.htpasswd username

# Add to Directory block in config
<Directory "/var/www/html/ecommerce">
    AuthType Basic
    AuthName "Restricted Access"
    AuthUserFile /etc/httpd/.htpasswd
    Require valid-user
</Directory>
```

## References
- Apache HTTP Server Documentation: https://httpd.apache.org/docs/
- Apache Virtual Host Documentation: https://httpd.apache.org/docs/current/vhosts/
- SELinux and Apache: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/configuring-selinux-for-applications-and-services_using-selinux
