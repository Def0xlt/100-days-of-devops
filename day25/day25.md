# Day 25: Git Merge Branches

## Task Description
The development team has completed work on a feature branch. Merge the feature branch into the main branch.

## Requirements
- Repository location: `/usr/src/kodekloudrepos/official`
- Two branches exist: `master` and `xfusioncorp_official`
- Merge `xfusioncorp_official` into `master`

## Solution

### 1. Navigate to repository:
```bash
cd /usr/src/kodekloudrepos/official
```

### 2. Switch to target branch (master):
```bash
git checkout master
```

### 3. Merge the feature branch:
```bash
git merge xfusioncorp_official
```

Expected output (if no conflicts):
```
Fast-forward
 file.txt | 10 ++++++++--
 1 file changed, 8 insertions(+), 2 deletions(-)
```

### 4. Optional - Delete merged branch:
```bash
git branch -d xfusioncorp_official
```

## Verification

```bash
# Verify current branch
git branch

# Check merge was successful
git log --oneline -5

# View graph
git log --oneline --graph --all -10

# List merged branches
git branch --merged
```

## Handling Merge Conflicts

If conflicts occur:
```bash
git merge xfusioncorp_official
# CONFLICT (content): Merge conflict in file.txt
```

**Resolve conflicts:**
```bash
# View conflicted files
git status

# Edit files to resolve conflicts
vi file.txt
# Remove conflict markers: <<<<<<< ======= >>>>>>>

# Mark as resolved
git add file.txt

# Complete merge
git commit
```

**Abort merge if needed:**
```bash
git merge --abort
```

## Common Merge Operations

```bash
# Basic merge
git checkout master
git merge feature-branch

# Merge with no fast-forward (creates merge commit)
git merge --no-ff feature-branch

# Squash merge (combine all commits)
git merge --squash feature-branch
git commit -m "Merge feature"

# Merge specific commit (cherry-pick)
git cherry-pick abc123
```

## Troubleshooting

**Not on target branch:**
```bash
git checkout master
git merge xfusioncorp_official
```

**Merge conflicts:**
```bash
# View conflicts
git status
git diff

# Resolve in files, then:
git add .
git commit
```

**Branch doesn't exist:**
```bash
# List branches
git branch -a

# Create if needed
git checkout -b xfusioncorp_official
```

**Uncommitted changes:**
```bash
# Stash changes
git stash

# Merge
git merge xfusioncorp_official

# Reapply changes
git stash pop
```

**Undo merge:**
```bash
# Before commit
git merge --abort

# After commit
git reset --hard HEAD~1
```

## Notes
- Merge combines changes from different branches
- Fast-forward: moves pointer forward (no merge commit)
- Three-way merge: creates merge commit with two parents
- Conflicts occur when same lines modified in both branches
- Always verify you're on correct target branch before merging

## Merge Workflow Example

```bash
# Start on master
git checkout master

# Pull latest changes (if remote)
git pull origin master

# Merge feature branch
git merge feature/new-api

# If conflicts, resolve them
# Then push
git push origin master

# Delete feature branch
git branch -d feature/new-api
```

## Conflict Resolution Example

Conflict markers in file:
```
<<<<<<< HEAD
content from master
=======
content from feature branch
>>>>>>> xfusioncorp_official
```

Remove markers and keep desired content:
```
final resolved content
```

Then:
```bash
git add file.txt
git commit
```

## Merge vs Rebase

**Merge:**
- Creates merge commit
- Preserves history
- Safer for shared branches

**Rebase:**
- Linear history
- Rewrites commits
- Use for local branches only
