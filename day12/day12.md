# Day 12: Linux Network Services

## Task Description
Diagnose why Apache on port 6200 at stapp01 is unreachable and restore connectivity without altering the existing index.html file.

## Solution Commands

### 1. Connect via SSH:
```bash
ssh tony@stapp01
```

### 2. Service and configuration checks:
```bash
sudo systemctl status httpd
sudo grep -n 'Listen' /etc/httpd/conf/httpd.conf
grep -R 'Listen' /etc/httpd/conf.d/
```

### 3. Socket verification:
```bash
ss -tuln | grep 6200
sudo netstat -tuln | grep 6200
```

### 4. Remove redundant Listen entries (if duplicates found):
```bash
# Example: Remove line 50 if it's a duplicate
sudo sed -i '50d' /etc/httpd/conf/httpd.conf
```

### 5. Restart Apache:
```bash
sudo systemctl restart httpd
```

### 6. Verify port binding:
```bash
ss -tuln | grep 6200
```

### 7. Firewall inspection and configuration:
```bash
sudo iptables -L -n --line-numbers
sudo iptables -I INPUT 1 -p tcp --dport 6200 -j ACCEPT
sudo service iptables save
```

### 8. Validate connectivity:
```bash
# From jump host
telnet stapp01 6200
curl http://stapp01:6200
```

## Troubleshooting Workflow

1. **Verify httpd service status** - Is the service running?
2. **Inspect Apache Listen directives** - Look for duplicates or misconfigurations
3. **Remove redundant Listen entries** - Use sed with confirmed line numbers
4. **Restart Apache** - Apply configuration changes
5. **Confirm port 6200 binding** - Use ss or netstat
6. **Check firewall rules** - Modify if necessary
7. **Validate connectivity** - Test from jump host

## Common Issues

- **Duplicate Listen directives** - Apache fails to start
- **Firewall blocking** - Service runs but unreachable
- **SELinux restrictions** - Port not allowed by policy
- **Port conflicts** - Another service using the same port

## Notes
**Critical constraint:** Do NOT modify the site's index.html file
- This requirement is emphasized as essential to task completion
- Focus on service configuration and network accessibility only
- Always backup configuration files before editing: `sudo cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.bak`
