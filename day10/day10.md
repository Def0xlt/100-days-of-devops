# Day 10: Linux Bash Scripts

## Task Description
Create a bash script on App Server 3 that archives website media files and transfers them to a remote backup server without requiring password authentication.

## Key Requirements
- Script location: `/scripts/media_backup.sh`
- Source directory: `/var/www/html/media`
- Local backup destination: `/backup/xfusioncorp_media.zip`
- Remote server: `stbkp01.stratos.xfusioncorp.com`
- Remote destination: `/backup`
- Constraint: No `sudo` commands within the script

## Solution

### 1. Install dependencies:
```bash
# RHEL/CentOS
sudo yum install -y zip

# Debian/Ubuntu
sudo apt-get install -y zip
```

### 2. Create the script:
```bash
sudo mkdir -p /scripts
sudo vi /scripts/media_backup.sh
```

### Script content:
```bash
#!/bin/bash

# Variables
SOURCE_DIR="/var/www/html/media"
LOCAL_BACKUP="/backup/xfusioncorp_media.zip"
REMOTE_SERVER="stbkp01.stratos.xfusioncorp.com"
REMOTE_DIR="/backup"

# Create local backup directory if it doesn't exist
mkdir -p /backup

# Validate source directory exists
if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: Source directory $SOURCE_DIR does not exist"
    exit 1
fi

# Create zip archive
zip -r "$LOCAL_BACKUP" "$SOURCE_DIR"

# Transfer to remote server
scp "$LOCAL_BACKUP" "${REMOTE_SERVER}:${REMOTE_DIR}/"

echo "Backup completed successfully"
```

### 3. Make script executable:
```bash
sudo chmod +x /scripts/media_backup.sh
```

### 4. SSH key configuration for passwordless authentication:
```bash
ssh-keygen -t rsa
ssh-copy-id user@stbkp01.stratos.xfusioncorp.com
```

### 5. Create backup directories:
```bash
sudo mkdir -p /backup
# On remote server: mkdir -p /backup
```

### 6. Verification:
```bash
/scripts/media_backup.sh
# Check both local and remote /backup directories
ls -lh /backup/
ssh user@stbkp01 "ls -lh /backup/"
```

## Script Breakdown

- **Variables** - Define paths for maintainability
- **Directory check** - Validates source exists before proceeding
- **zip -r** - Recursively compresses the directory
- **scp** - Securely copies file to remote server
- **Error handling** - Exits if source directory missing

## Notes
- The script uses fixed paths and overwrites previous backups with each execution
- Firewall rules must permit SSH connections between servers
- Automated scheduling (cron) requires the same user account that holds the SSH credentials
- Both local and remote `/backup` directories must exist and be writable
- Consider adding timestamped backups: `xfusioncorp_media_$(date +%Y%m%d).zip`
