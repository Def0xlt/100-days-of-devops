# Day 16: Install and Configure Nginx as a Load Balancer

## Task Description
Configure Nginx on the load balancer server (stlb01) to distribute HTTP traffic across three application servers using round-robin load balancing. The backend application servers are already running Apache web servers. The goal is to set up Nginx as a reverse proxy load balancer to route traffic efficiently across all three app servers.

## Solution Commands

### 1. SSH into the load balancer server:
```bash
ssh user@stlb01
# Or using the IP address
ssh user@172.16.238.14
```

### 2. Install Nginx:
```bash
# For RHEL/CentOS based systems
sudo yum update -y
sudo yum install -y nginx

# For Debian/Ubuntu based systems
sudo apt update
sudo apt install -y nginx
```

### 3. Backup original Nginx configuration:
```bash
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
```

### 4. Edit Nginx configuration:
```bash
sudo vi /etc/nginx/nginx.conf
```

Add or modify the configuration inside the `http` block:

```nginx
http {
    # ... existing configuration ...

    # Define upstream backend servers
    upstream backend_servers {
        server stapp01:80;
        server stapp02:80;
        server stapp03:80;
    }

    # Server block for load balancer
    server {
        listen 80;
        server_name _;

        location / {
            proxy_pass http://backend_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

    # ... rest of configuration ...
}
```

**Vi editor commands:**
- Press `i` to enter insert mode
- Add/modify the configuration
- Press `Esc` to exit insert mode
- Type `:wq` and press Enter to save and quit

### 5. Test Nginx configuration:
```bash
sudo nginx -t
```

Expected output: `nginx: configuration file /etc/nginx/nginx.conf test is successful`

### 6. Start and enable Nginx:
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
```

### 7. Configure firewall (if firewall is active):
```bash
# For firewalld
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload

# For UFW (Ubuntu/Debian)
sudo ufw allow 80/tcp
sudo ufw reload
```

### 8. Verify load balancer is working:
```bash
# Check if Nginx is listening on port 80
sudo netstat -tuln | grep :80
sudo ss -tuln | grep :80

# Test load balancer from localhost
curl http://localhost/

# Test multiple times to verify round-robin
curl http://www.stlb01:80

# Test with verbose output to see which backend responds
curl -v http://172.16.238.14/
```
## Troubleshooting Steps

1. **Check Nginx status** - Is the service active and running?
2. **Verify backend connectivity** - Can the load balancer reach all three app servers?
3. **Test configuration** - Use `nginx -t` before starting/reloading
4. **Check Apache status** - Ensure all backend servers have Apache running
5. **Review error logs** - Check `/var/log/nginx/error.log`
6. **Verify ports** - Ensure port 80 is not already in use
7. **Firewall rules** - Make sure port 80 is open on the load balancer
8. **SELinux context** - If SELinux is enabled, check for denials in audit logs

## Success Indicators
- Nginx service status shows "Active: active (running)"
- Port 80 shows LISTEN status on stlb01
- `curl http://172.16.238.14/` returns content from backend servers
- Multiple curl requests show round-robin distribution (check access logs)
- No errors in `nginx -t` output
- Apache remains running on all three app servers (stapp01, stapp02, stapp03)

## Common Obstacles
- **Port 80 already in use:** Check if another service is using port 80 with `netstat` or `ss`
- **Backend unreachable:** Verify network connectivity and firewall rules on backend servers
- **SELinux blocking:** Use `setsebool -P httpd_can_network_connect on` to allow Nginx to connect to backends
- **Configuration syntax errors:** Missing semicolons, braces, or incorrect upstream syntax
- **Apache not running:** Start Apache on backend servers before testing load balancer
- **Firewall blocking:** Ensure port 80 is open on both load balancer and backend servers

## Notes
- Default load balancing method is round-robin (no additional configuration needed)
- The `upstream` directive defines the backend server pool
- Proxy headers preserve client information when forwarding requests
- Only modify `/etc/nginx/nginx.conf` as per task requirements
- Do not change Apache port configurations on backend servers
- Apache typically listens on port 80 by default
- Use `journalctl -xe -u nginx` for detailed service logs
- Always test configuration with `nginx -t` before reloading
- Nginx can be reloaded without downtime using `systemctl reload nginx`

## Load Balancing Methods
While this task uses the default round-robin method, Nginx supports other algorithms:
- **round-robin** (default): Distributes requests evenly across servers
- **least_conn**: Sends requests to server with fewest active connections
- **ip_hash**: Same client IP always goes to same backend server
- **weight**: Assigns different weights to servers for uneven distribution

Example with weights:
```nginx
upstream backend_servers {
    server stapp01:80 weight=3;
    server stapp02:80 weight=2;
    server stapp03:80 weight=1;
}
```

## Proxy Headers Explained
- **Host:** Preserves the original host header from client request
- **X-Real-IP:** Contains the actual client IP address
- **X-Forwarded-For:** Chain of proxy IPs (useful for multiple proxies)
- **X-Forwarded-Proto:** Original protocol (http or https)

## Health Checks
For production environments, consider adding health checks to automatically remove failed backends:
```nginx
upstream backend_servers {
    server stapp01:80 max_fails=3 fail_timeout=30s;
    server stapp02:80 max_fails=3 fail_timeout=30s;
    server stapp03:80 max_fails=3 fail_timeout=30s;
}
```

## SELinux Configuration
If SELinux is enforcing and blocking Nginx from connecting to backends:
```bash
# Check SELinux status
getenforce

# Allow Nginx to make network connections
sudo setsebool -P httpd_can_network_connect on

# Check for SELinux denials
sudo ausearch -m avc -ts recent
```

## Verification Commands Summary
```bash
# On Load Balancer (stlb01)
sudo systemctl status nginx
sudo nginx -t
sudo netstat -tuln | grep :80
curl http://localhost/

# Test round-robin
for i in {1..9}; do curl http://172.16.238.14/; done

# Check logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```
