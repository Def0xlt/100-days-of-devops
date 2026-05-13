# Day 21: Git Clone Repositories

## Task Description
The development team wants to start using Git repositories for their development work. Clone the Git repository to the development server.

## Requirements
- Git is already installed on the storage server
- Clone the repository `/opt/official.git` to `/usr/src/kodekloudrepos`

## Solution

### 1. Create target directory:
```bash
sudo mkdir -p /usr/src/kodekloudrepos
```

### 2. Clone the repository:
```bash
sudo git clone /opt/official.git /usr/src/kodekloudrepos
```

Expected output: `Cloning into '/usr/src/kodekloudrepos'...`

### 3. Set ownership (if needed):
```bash
sudo chown -R $USER:$USER /usr/src/kodekloudrepos
```

## Verification

```bash
# Check directory exists
ls -la /usr/src/kodekloudrepos

# Verify it's a Git repository
cd /usr/src/kodekloudrepos
git status

# Check remote configuration
git remote -v
```

Expected output:
```
origin  /opt/official.git (fetch)
origin  /opt/official.git (push)
```

## Common Clone Operations

```bash
# Clone to current directory with default name
git clone /opt/official.git

# Clone to specific directory
git clone /opt/official.git /path/to/destination

# Clone specific branch
git clone -b branch-name /opt/official.git /path/to/destination

# Shallow clone (faster for large repos)
git clone --depth 1 /opt/official.git /path/to/destination

# Clone from remote server
git clone user@server:/opt/official.git
```

## Troubleshooting

**Permission denied:**
```bash
sudo git clone /opt/official.git /usr/src/kodekloudrepos
sudo chown -R $USER:$USER /usr/src/kodekloudrepos
```

**Directory already exists:**
```bash
sudo rm -rf /usr/src/kodekloudrepos
sudo git clone /opt/official.git /usr/src/kodekloudrepos
```

**Repository not found:**
```bash
# Verify source repository exists
ls -la /opt/official.git
```

## Notes
- Clone creates a complete copy with all history
- Automatically sets up remote named "origin"
- Creates `.git` directory with version control data
- Can clone from local paths or remote URLs
