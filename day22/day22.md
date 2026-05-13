# Day 22: Git Fork a Repository

## Task Description
A new developer joined the team and needs a copy of the repository to work independently. Create a fork of the existing repository.

## Requirements
- Bare repository exists at `/opt/games.git`
- Create a fork at `/opt/games_fork.git`
- The fork should also be a bare repository

## Solution

### 1. Fork by cloning as bare:
```bash
sudo git clone --bare /opt/games.git /opt/games_fork.git
```

Expected output: `Cloning into bare repository '/opt/games_fork.git'...`

### 2. Set permissions (if needed):
```bash
sudo chown -R $USER:$USER /opt/games_fork.git
```

### 3. Optional - Remove origin to make it independent:
```bash
cd /opt/games_fork.git
sudo git remote remove origin
```

## Verification

```bash
# Check fork exists
ls -la /opt/games_fork.git

# Verify it's bare
cd /opt/games_fork.git
git config --get core.bare
```

Expected: `true`

```bash
# Compare with original
du -sh /opt/games.git /opt/games_fork.git
```

## Using the Fork

### Clone the fork for development:
```bash
git clone /opt/games_fork.git ~/my-project
cd ~/my-project
```

### Keep fork synced with original:
```bash
cd ~/my-project

# Add original as upstream
git remote add upstream /opt/games.git

# Fetch from upstream
git fetch upstream

# Merge upstream changes
git merge upstream/master

# Push to your fork
git push origin master
```

## Troubleshooting

**Source repository not found:**
```bash
ls -la /opt/games.git
cd /opt/games.git
git config --get core.bare
```

**Destination already exists:**
```bash
sudo rm -rf /opt/games_fork.git
sudo git clone --bare /opt/games.git /opt/games_fork.git
```

**Permission denied:**
```bash
sudo git clone --bare /opt/games.git /opt/games_fork.git
sudo chown -R $USER:$USER /opt/games_fork.git
```

## Notes
- `--bare` creates a repository without working directory
- `--mirror` is alternative that preserves all refs (for exact copies)
- Forking allows independent development
- Use upstream remote to sync with original repository

## Fork vs Clone vs Branch

- **Fork**: Server-side copy, independent development
- **Clone**: Local copy, maintains link to origin
- **Branch**: Same repository, lightweight feature development
