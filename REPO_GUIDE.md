# REPO_GUIDE.md - NewLens Repository Structure

**Last Updated:** 2026-02-18  
**Status:** PRODUCTION

---

## 🎯 The Clean Structure

We learned the hard way that multiple overlapping repos cause regressions. This is our new "Gold Tier" structure:

### **WEBSITE**
```
📦 newlens-uk/website-test
   ├── Domain: test.newlens.uk
   ├── Purpose: Testing all website changes before production
   ├── Branch: main (auto-deploys to Vercel)
   └── Update frequency: Multiple times daily

📦 newlens-uk/website-prod
   ├── Domain: prod.newlens.uk (will become www.newlens.uk)
   ├── Purpose: Production website - customer-facing
   ├── Branch: main (protected, requires PR approval)
   └── Update frequency: Weekly releases
```

### **PORTAL**
```
📦 newlens-uk/portal-test
   ├── Domain: portal-test.newlens.uk
   ├── Purpose: Testing portal features and integrations
   ├── Branch: main (auto-deploys to Vercel)
   └── Update frequency: Multiple times daily

📦 newlens-uk/portal-prod
   ├── Domain: portal.newlens.uk
   ├── Purpose: Production portal - client access
   ├── Branch: main (protected, requires PR approval)
   └── Update frequency: Bi-weekly releases
```

---

## 🛡️ Safety Rules

### **Rule 1: Test-First Deployment**
- ALL changes go to `-test` environment first
- Test thoroughly before moving to `-prod`
- Never push directly to production repos

### **Rule 2: Git Tag Restore Points**
Before ANY change to a production repo:
```bash
git tag backup-$(date +%Y%m%d-%H%M%S)
git push --tags
```

### **Rule 3: Archived Repos Are Sacred**
The `_ARCHIVE_2026-02-18_*` repos in the workspace are NEVER deleted. They are our insurance policy.

### **Rule 4: Know Where You Are**
Before running ANY command:
```bash
pwd           # Where am I?
git status    # What repo is this?
git log -1    # Is this the right version?
```

---

## 📂 Local Workspace Structure

```
~/.openclaw/workspace/
├── website-test/          ← Active: Edit here for testing
├── website-prod/          ← Protected: PR only
├── portal-test/           ← Active: Edit here for testing
├── portal-prod/           ← Protected: PR only
│
├── _ARCHIVE_2026-02-18_newlens-v2/           ← NEVER DELETE
├── _ARCHIVE_2026-02-18_newlens-v2-preview/   ← NEVER DELETE (Source of Truth)
├── _ARCHIVE_2026-02-18_newlens-portal/       ← NEVER DELETE
│
└── backups/
    ├── ARCHIVE_newlens-v2_20260218-124125.tar.gz
    ├── ARCHIVE_newlens-v2-preview_20260218-124125.tar.gz
    └── ARCHIVE_newlens-portal_20260218-124125.tar.gz
```

---

## 🔄 Update Workflow

See `WORKFLOW.md` for detailed step-by-step process.

**Quick Reference:**
1. Make changes in `-test` repo
2. Push to GitHub
3. Verify on test domain
4. Create PR to `-prod`
5. Review + merge
6. Verify on prod domain

---

## 🚨 Emergency Rollback

If something breaks:

### **Option 1: Git Revert**
```bash
cd website-prod/  # or portal-prod
git log --oneline  # Find the last good commit
git revert <bad-commit-hash>
git push
```

### **Option 2: Restore from Tag**
```bash
git tag  # List all backup tags
git reset --hard backup-YYYYMMDD-HHMMSS
git push --force
```

### **Option 3: Nuclear Option (Restore from Archive)**
```bash
cd ~/.openclaw/workspace/
tar -xzf backups/ARCHIVE_newlens-v2-preview_20260218-124125.tar.gz
# Then manually copy files to the affected repo
```

---

## ❌ What We DON'T Do Anymore

- ❌ Edit files without checking `git status` first
- ❌ Work in multiple repos with similar names
- ❌ Push directly to production without testing
- ❌ Delete old repos (we archive them)
- ❌ Make "quick fixes" without creating a restore point

---

## ✅ What We DO Now

- ✅ Always work in the correct `-test` repo
- ✅ Create git tags before major changes
- ✅ Test everything on test domain first
- ✅ Use PRs for production changes
- ✅ Keep archives and backups forever
- ✅ Document every major change

---

**This structure was born from a near-disaster on 2026-02-18. We will never make that mistake again.** 🛡️
