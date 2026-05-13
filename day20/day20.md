# Day 20: Git Install and Create Bare Repository

## Task Description
The Nautilus development team shared requirements with the DevOps team to start using Git for their project management. Set up a Git repository on the storage server in the Stratos DC.

## Requirements
- Install `git` package on the storage server using `yum`
- Create a bare Git repository named `/opt/official.git`

## Solution

### 1. Install Git:
```bash
sudo yum install -y git

# Verify installation
git --version
```

### 2. Create bare repository:
```bash
sudo git init --bare /opt/official.git
```

Expected output: `Initialized empty Git repository in /opt/official.git/`

### 3. Set permissions (if needed):
```bash
sudo chown -R $USER:$USER /opt/official.git
```

## Verification

```bash
# Check repository exists
ls -la /opt/official.git

# Verify it's bare
cd /opt/official.git
git config --get core.bare
```

Expected: `true`

## Using the Bare Repository

### Clone it:
```bash
git clone /opt/official.git myproject
cd myproject
```

### Push to it:
```bash
# In your project directory
echo "# Project" > README.md
git add README.md
git commit -m "Initial commit"
git remote add origin /opt/official.git
git push -u origin master
```

## Troubleshooting

**Permission denied:**
```bash
sudo git init --bare /opt/official.git
sudo chown -R $USER:$USER /opt/official.git
```

**Repository already exists:**
```bash
sudo rm -rf /opt/official.git
sudo git init --bare /opt/official.git
```

## Notes
- Bare repositories have no working directory, only version control data
- Used as central repositories for collaboration
- Convention: name with `.git` suffix
- Cannot make commits directly in bare repos (only push/pull)

## Quick Reference

**What is a bare repository?**
- Contains only `.git` contents (no working files)
- Used for central/shared repositories
- Accessed via clone/push/pull operations

**Bare vs Non-Bare:**
```
# Non-bare: myproject/.git/ + working files
# Bare: myproject.git/ (just version control data)
```
