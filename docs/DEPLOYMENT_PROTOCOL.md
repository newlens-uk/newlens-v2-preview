# Deployment Protocol - newlens Website

**THIS IS THE SINGLE SOURCE OF TRUTH FOR DEPLOYMENTS**

## Repository Structure

**There is ONLY ONE repository that matters for production:**

- **Repository**: `newlens-uk/newlens-v2-preview`
- **Branch**: `main`
- **Vercel Project**: Connected to this repo
- **Live URL**: `https://v2-site-sigma.vercel.app/`

**DO NOT USE**: `newlens-uk/newlens-v2` - This is obsolete and should be ignored.

## Working Directory

**Single workspace location:**
```
~/.openclaw/workspace/newlens-v2-preview/
```

**Always work in this directory. Never create new clones.**

## Deployment Workflow

### 1. Check Current State
```bash
cd ~/.openclaw/workspace/newlens-v2-preview
git status
git log --oneline -5
```

### 2. Make Changes
Edit files directly in this directory. Never copy from other locations unless absolutely necessary.

### 3. Preview Changes Locally
```bash
# Check what files changed
git diff

# Review specific file
git diff index.html
```

### 4. Stage & Commit
```bash
# Stage SPECIFIC files only (never use 'git add .')
git add index.html alpha-partner.html assessment-new.html

# Commit with clear message
git commit -m "Brief description of what changed"
```

### 5. Push to Production
```bash
# Always pull first to check for conflicts
git pull origin main

# If conflicts: STOP and resolve manually
# If clean: push
git push origin main
```

### 6. Verify Deployment
```bash
# Wait 30-45 seconds for Vercel CI/CD
sleep 40

# Check live site
curl -s "https://v2-site-sigma.vercel.app/index.html" | grep "some-unique-text-from-your-change"
```

## Rules

1. **Never clone a new repo** - Use the existing `newlens-v2-preview-deploy/` directory
2. **Never use `git add .`** - Always add specific files
3. **Never force push** unless you're 100% certain (and document why)
4. **Always pull before push** to catch conflicts early
5. **One logical change per commit** - Don't bundle unrelated fixes
6. **Test changes locally first** if possible (open HTML files in browser)
7. **Verify deployment** after every push

## If Something Goes Wrong

### Conflict on Pull
```bash
# See what's conflicting
git status

# Decide: merge or reset
# Option A: Merge (if you want to keep both changes)
git merge origin/main
# Fix conflicts manually, then:
git add <fixed-files>
git commit -m "Merge conflict resolution"

# Option B: Reset (if remote is correct)
git reset --hard origin/main
# Then reapply your changes
```

### Wrong Files Pushed
```bash
# Check recent commits
git log --oneline -10

# Revert to good commit (replace HASH with the good commit)
git reset --hard <GOOD_COMMIT_HASH>

# Force push (document in commit why)
git push --force origin main
```

### Lost Changes
```bash
# Find lost commits
git reflog

# Restore from reflog
git reset --hard <COMMIT_HASH>
```

## Quick Reference Card

### Daily Workflow
```bash
cd ~/.openclaw/workspace/newlens-v2-preview
# 1. Pull latest
git pull origin main

# 2. Make changes
# ... edit files ...

# 3. Check what changed
git status
git diff

# 4. Stage specific files
git add file1.html file2.html

# 5. Commit
git commit -m "Description"

# 6. Push
git push origin main

# 7. Wait & verify
sleep 40 && curl -s "https://v2-site-sigma.vercel.app/" | head -20
```

### Emergency Reset
```bash
cd ~/.openclaw/workspace/newlens-v2-preview
git fetch origin
git reset --hard origin/main
```

## File Locations

**Key files in the repo:**
- `index.html` - Homepage
- `alpha-partner.html` - Alpha Partner form
- `assessment-new.html` - Assessment form
- `contact.html` - Contact page
- `privacy.html` - Privacy policy

**Never edit on production** - always edit in the local workspace and push.

---

**Last Updated**: 2026-02-11
**Version**: 1.0
