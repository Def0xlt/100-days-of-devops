# Day 01: Create a User with a Non-Interactive Shell

## Task Description
Create a user account named `mariyam` on App Server 2 that cannot access an interactive login shell, meeting requirements for a backup agent tool.

## Solution Commands

### Primary solution:
```bash
sudo useradd mariyam -s /sbin/nologin
```

### Alternative approach:
```bash
sudo adduser mariyam --shell /sbin/nologin
```

### Distribution-specific variations:

**RHEL/CentOS/Alma/Rocky:**
```bash
sudo useradd -M -s /sbin/nologin mariyam
```

**Debian/Ubuntu:**
```bash
sudo adduser --disabled-password --gecos "" --shell /usr/sbin/nologin mariyam
```

### Modifying existing users:
```bash
sudo usermod -s /sbin/nologin mariyam
sudo chsh -s /sbin/nologin mariyam
sudo usermod -s /bin/false mariyam
```

### Security enhancement:
```bash
sudo passwd -l mariyam
```

### Verification commands:
```bash
getent passwd mariyam
grep -E '^(mariyam:)' /etc/passwd
su - mariyam
```

## Notes
- Shell path varies by distribution (`/sbin/nologin` vs. `/usr/sbin/nologin`)
- `/bin/false` serves as an alternative in minimal environments
- Non-interactive accounts suit service users
- The final verification command should deny access with a message indicating the account is unavailable
- Can be automated using Ansible playbooks or cloud-init configurations
