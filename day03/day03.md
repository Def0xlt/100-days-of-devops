# Day 03: Secure Root SSH Access

## Task Description
Disable direct SSH root login across all application servers (stapp01, stapp02, stapp03) in the Stratos Datacenter following new security protocols from xFusionCorp Industries.

## Solution Commands

### 1. Connect to each application server:
```bash
ssh user@stapp01
ssh user@stapp02
ssh user@stapp03
```

### 2. Modify SSH configuration:
```bash
sudo vi /etc/ssh/sshd_config
```

### 3. Set the required directive:
```
PermitRootLogin no
```

### 4. Apply the changes:
```bash
sudo systemctl restart sshd
```

## Security Rationale

**Why disable root SSH login:**
- Bypasses user-level accountability
- Exposes the most privileged account to brute-force attacks
- Makes auditing and tracking changes more difficult

The organization is implementing least privilege principles by requiring administrative users to leverage named accounts with sudo privileges instead of direct root access.

## Notes
- This change must be applied to all three application servers
- After implementation, administrators must use their personal accounts and sudo for privileged operations
- Verify the change doesn't lock you out before disconnecting
- Always keep an active session open when testing SSH configuration changes
