# Day 09: MariaDB Troubleshooting

## Task Description
Resolve MariaDB service failures caused by inability to create its PID directory under /run (a temporary filesystem). The solution requires establishing a persistent tmpfiles rule, configuring proper ownership and SELinux contexts, and ensuring the service remains enabled.

## Root Cause Analysis
- /run is a tmpfs cleaned on reboot
- MariaDB requires /run/mariadb to exist with mysql:mysql ownership
- Missing directories or incorrect SELinux labels prevent PID file creation, causing startup failures

## Solution Commands

### 1. Create directory with proper permissions:
```bash
sudo mkdir -p /run/mariadb
sudo chmod 0755 /run/mariadb
sudo chown mysql:mysql /run/mariadb
```

### 2. SELinux configuration (if active):
```bash
sudo semanage fcontext -a -t mysqld_var_run_t "/run/mariadb(/.*)?"
sudo restorecon -Rv /run/mariadb
```

### 3. Create persistent tmpfiles rule:
```bash
sudo bash -c 'cat > /etc/tmpfiles.d/mariadb.conf << EOF
d /run/mariadb 0755 mysql mysql -
EOF'
```

### 4. Service management:
```bash
sudo systemctl enable mariadb
sudo systemctl start mariadb
sudo systemctl status mariadb
```

## Verification Steps

Post-reboot checks should confirm:
- MariaDB service is active and running
- /run/mariadb exists and is owned by mysql:mysql

```bash
ls -ld /run/mariadb
systemctl status mariadb
```

## Understanding tmpfiles.d

The tmpfiles.d configuration format:
```
d /run/mariadb 0755 mysql mysql -
│ │            │    │     │     │
│ │            │    │     │     └─── Age (- means no automatic cleanup)
│ │            │    │     └───────── Group
│ │            │    └─────────────── User
│ │            └──────────────────── Permissions
│ └───────────────────────────────── Path
└─────────────────────────────────── Type (d = directory)
```

## Notes
- The tmpfiles.d configuration ensures the directory is automatically recreated after each system reboot
- SELinux utilities (semanage, restorecon) are required when SELinux is active
- This is a common issue with services that store runtime data in /run
- Always check journal logs for detailed error messages: `journalctl -u mariadb -xe`
