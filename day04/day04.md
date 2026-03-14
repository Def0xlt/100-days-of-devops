# Day 04: Script Execution Permissions

## Task Description
Enable execution permissions for the `xfusioncorp.sh` bash script located at `/tmp/xfusioncorp.sh` on App Server 1, making it executable by all users to enable automated backup processes.

## Solution Commands

### Access the server:
```bash
ssh tony@<App-Server-1-IP>
```

### Grant executable permissions:
```bash
sudo chmod +x /tmp/xfusioncorp.sh
```

### Ensure universal execution access:
```bash
sudo chmod a+x /tmp/xfusioncorp.sh
```

### Alternative approach using octal notation:
```bash
sudo chmod 755 /tmp/xfusioncorp.sh
```

## Understanding Permissions

### Permission breakdown:
- `+x` - Adds execute permission
- `a+x` - Adds execute permission for all (owner, group, others)
- `755` - Owner: read/write/execute, Group: read/execute, Others: read/execute

### Verify permissions:
```bash
ls -l /tmp/xfusioncorp.sh
```

Expected output: `-rwxr-xr-x` (755) or `-rwxrwxrwx` (777)

## Notes
- Organization: xFusionCorp Industries (training scenario)
- Location: Stratos Datacenter, App Server 1
- Problem: The script lacks proper executable permissions
- Learning value: Demonstrates Linux file permission management and the importance of proper access controls for deployment automation
- Making scripts executable ensures they can run when invoked by users or automated services
