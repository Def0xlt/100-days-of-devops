# Day 23: Git Manage Remotes

## Task Description
A developer accidentally removed the remote repository connection. Add the remote repository back and verify the setup.

## Requirements
- Cloned repository exists at `/usr/src/kodekloudrepos/official`
- Original bare repository is at `/opt/official.git`
- Add remote named `origin` pointing to `/opt/official.git`

## Solution

### 1. Navigate to repository:
```bash
cd /usr/src/kodekloudrepos/official
```

### 2. Check current remotes:
```bash
git remote -v
```

### 3. Add the remote:
```bash
git remote add origin /opt/official.git
```

### 4. Verify remote was added:
```bash
git remote -v
```

Expected output:
```
origin  /opt/official.git (fetch)
origin  /opt/official.git (push)
```

## Verification

```bash
# List remote names
git remote

# Show detailed remote info
git remote show origin

# Get remote URL
git config --get remote.origin.url

# Test connection
git fetch origin
```

## Common Remote Operations

```bash
# Add remote
git remote add origin /opt/official.git
git remote add upstream /opt/upstream.git

# Change remote URL
git remote set-url origin /new/path/to/repo.git

# Remove remote
git remote remove origin

# Rename remote
git remote rename origin upstream

# List all remotes with URLs
git remote -v
```

## Troubleshooting

**Remote already exists:**
```bash
# Remove existing remote
git remote remove origin

# Add it again
git remote add origin /opt/official.git
```

**Not in a Git repository:**
```bash
# Verify you're in the right directory
cd /usr/src/kodekloudrepos/official
ls -la .git
```

**Repository not found:**
```bash
# Verify source repository exists
ls -la /opt/official.git
```

**Cannot fetch/push:**
```bash
# Check remote URL is correct
git remote -v

# Update if wrong
git remote set-url origin /correct/path/to/repo.git
```

## Notes
- `origin` is conventional name for primary remote
- `upstream` typically refers to original source in fork workflows
- Repositories can have multiple remotes
- Remote configurations stored in `.git/config`
- Can use local paths or network URLs (SSH, HTTPS, Git protocol)

## Multiple Remotes Example

```bash
# Add your fork as origin
git remote add origin /opt/my-fork.git

# Add original as upstream
git remote add upstream /opt/official.git

# Fetch from upstream
git fetch upstream

# Merge upstream changes
git merge upstream/master

# Push to your fork
git push origin master
```
