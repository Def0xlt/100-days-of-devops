# Day 14: Linux Process Troubleshooting

## Task Description
Locate a non-functional Apache instance across Stratos DC app servers and restore it to operational status on port 6400.

## Solution Commands

### 1. Initial diagnostics:
```bash
cat /etc/os-release
sudo systemctl status httpd
```

### 2. Configuration verification:
```bash
grep -n 6400 /etc/httpd/conf/httpd.conf
grep -R 'Listen 6400' /etc/httpd/conf.d/
```

### 3. Add/verify Listen directive:
```bash
sudo vi /etc/httpd/conf/httpd.conf
# Add: Listen 6400
```

### 4. Configuration syntax test:
```bash
sudo httpd -t
```

Expected output: `Syntax OK`

### 5. SELinux port configuration (if needed):
```bash
getenforce
sudo semanage port -a -t http_port_t -p tcp 6400
```

### 6. Service restart and validation:
```bash
sudo systemctl restart httpd
sudo systemctl status httpd
```

### 7. Socket verification:
```bash
ss -tuln | grep 6400
sudo netstat -luntp | grep 6400
```

Expected output: `LISTEN` state on port 6400

### 8. Journal inspection:
```bash
journalctl -u httpd.service -xe
```

### 9. Port conflict detection:
```bash
sudo netstat -luntp | grep 6400
# If another process is using the port
sudo kill <PID>
```

## Troubleshooting Steps

1. **Check service status** - Is httpd running or failed?
2. **Verify Listen directive** - Is port 6400 configured?
3. **Test configuration** - Use `httpd -t` for syntax check
4. **Check SELinux** - Is the port allowed by policy?
5. **Restart service** - Apply configuration changes
6. **Verify port binding** - Is Apache listening on 6400?
7. **Check logs** - Any errors in journalctl?
8. **Port conflicts** - Is another process using 6400?

## Success Indicators
- Service displays "Active: active (running)"
- Port 6400 shows LISTEN status
- Recent journal entries contain no critical errors
- HTTP requests succeed: `curl http://localhost:6400`

## Common Obstacles
- **Port conflicts:** Demand process termination or reassignment
- **Configuration syntax errors:** Surface through `httpd -t`
- **SELinux restrictions:** Necessitate explicit port policy additions using semanage
- **Missing Listen directive:** Apache won't bind to the port

## Notes
- The Listen directive must be present: `Listen 6400` in the Apache configuration
- For SELinux environments, port registration is required
- Configuration should be validated before restarting the service
- Always check logs when troubleshooting service failures
- Use `systemctl status httpd -l` for full status without truncation
