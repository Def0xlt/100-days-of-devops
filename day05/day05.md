# Day 05: SELinux Installation and Configuration

## Task Description
The security team at xFusionCorp Industries requires SELinux installation and permanent disabling on App Server 2. The configuration must persist after the scheduled reboot, though immediate restart is not necessary.

## Solution Commands

### Installation (RHEL/CentOS/Amazon Linux):
```bash
sudo yum install -y selinux-policy selinux-policy-targeted
```

### Installation (Debian/Ubuntu):
```bash
sudo apt-get install -y selinux selinux-utils selinux-policy-default
```

### Configuration file edit:
```bash
sudo vi /etc/selinux/config
```

Change `SELINUX=enforcing` to `SELINUX=disabled`

### Verification:
```bash
grep SELINUX= /etc/selinux/config
```

Expected output: `SELINUX=disabled`

## SELinux Modes

- **Enforcing** - SELinux policy is enforced, denying access and logging actions
- **Permissive** - SELinux policy is not enforced, only logs violations
- **Disabled** - SELinux is completely disabled

## Notes
- The configuration file modification ensures SELinux remains disabled after reboot
- Runtime status tools like `getenforce` reflect current session state only, not post-reboot configuration
- No immediate reboot is required; scheduled maintenance will handle this
- Expected verification output: `SELINUX=disabled`
- In production environments, consider using Permissive mode first for testing before fully disabling
