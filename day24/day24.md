# Day 24: Git Create Branches

## Task Description
The development team is working on multiple features simultaneously. Create a new Git branch to isolate work for a specific feature.

## Requirements
- Repository location: `/usr/src/kodekloudrepos/official`
- Create a new branch named `xfusioncorp_official`

## Solution

### 1. Navigate to repository:
```bash
cd /usr/src/kodekloudrepos/official
```

### 2. Create the branch:
```bash
git branch xfusioncorp_official
```

**Or create and switch in one command:**
```bash
git checkout -b xfusioncorp_official
```

### 3. Verify branch was created:
```bash
git branch
```

Expected output:
```
* master
  xfusioncorp_official
```

The `*` indicates current branch.

## Verification

```bash
# List all branches
git branch

# List with commit info
git branch -v

# Show current branch
git status
```

## Common Branch Operations

```bash
# Create branch
git branch feature-name

# Create and switch to branch
git checkout -b feature-name

# Switch to existing branch
git checkout branch-name

# List local branches
git branch

# List all branches (local and remote)
git branch -a

# Delete branch
git branch -d branch-name

# Force delete branch
git branch -D branch-name

# Rename current branch
git branch -m new-name
```

## Troubleshooting

**Branch already exists:**
```bash
# Delete existing branch
git branch -d xfusioncorp_official

# Create again
git branch xfusioncorp_official
```

**Uncommitted changes blocking switch:**
```bash
# Stash changes
git stash

# Create/switch branch
git checkout -b xfusioncorp_official

# Reapply changes
git stash pop
```

**Not in a Git repository:**
```bash
cd /usr/src/kodekloudrepos/official
ls -la .git
```

## Notes
- Branches are lightweight pointers to commits
- Creating a branch doesn't change working directory
- Use `git checkout` or `git switch` to change branches
- Branch names stored in `.git/refs/heads/`

## Basic Workflow

```bash
# Create feature branch
git checkout -b feature/new-api

# Make changes
echo "new feature" > feature.txt
git add feature.txt
git commit -m "Add new feature"

# Switch back to master
git checkout master

# Merge feature (covered in Day 25)
git merge feature/new-api
```

## Branch Naming Conventions

```bash
# Features
git branch feature/user-login
git branch feature/payment

# Bug fixes
git branch bugfix/login-error
git branch fix/memory-leak

# Hotfixes
git branch hotfix/security-patch

# Releases
git branch release/v1.2.0
```
