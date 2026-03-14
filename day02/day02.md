# Day 02: Temporary User Setup with Expiry

## Task Description
Create a user named `rose` on App Server 2 in Stratos Datacenter with an expiration date of February 17, 2026, using lowercase naming conventions. A developer needs temporary access for a limited duration on the Nautilus project.

## Solution Commands

### User creation with expiry:
```bash
sudo useradd -e 2026-02-17 rose
```

### Verification:
```bash
sudo chage -l rose
```

## Implementation Steps
1. Connect to App Server 2 via SSH using lab-provided credentials (typically `steve@stapp02`)
2. Execute the useradd command with the expiry flag to create the temporary account
3. Run the chage command to confirm proper configuration and view expiration settings

## Notes
- Username: rose (lowercase)
- Server: App Server 2
- Expiry Date: 2026-02-17
- Location: Stratos Datacenter
- The chage command displays account expiry, password expiry, and more
- Useful for temporary contractors or limited-duration access requirements
