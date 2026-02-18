# WORKFLOW.md - Safe Change Management

**Last Updated:** 2026-02-18  
**Owner:** Newt (AI Agent) + Mat Keys

---

## 🎯 The Golden Rule

> **Test-First, Production-Last**  
> Every single change goes through the test environment before production. No exceptions.

---

## 📋 Standard Website Update Workflow

### **Phase 1: Preparation**

1. **Verify Location**
   ```bash
   cd ~/.openclaw/workspace/website-test
   pwd  # Confirm you're in the right place
   git status  # Should be clean
   ```

2. **Pull Latest**
   ```bash
   git pull origin main
   ```

3. **Create Restore Point**
   ```bash
   git tag backup-$(date +%Y%m%d-%H%M%S)
   git push --tags
   ```

### **Phase 2: Make Changes**

4. **Edit Files**
   - Update content, fix bugs, add features
   - Keep changes focused and atomic

5. **Test Locally** (if applicable)
   - Open files in browser to preview

6. **Commit Changes**
   ```bash
   git add .
   git commit -m "feat: descriptive message about what changed"
   ```

### **Phase 3: Deploy to Test**

7. **Push to GitHub**
   ```bash
   git push origin main
   ```

8. **Verify on Test Domain**
   - Open `https://test.newlens.uk` in browser
   - Test all changed functionality
   - Check on mobile view (if applicable)
   - Get Mat's approval if it's a significant change

### **Phase 4: Promote to Production**

9. **Create Pull Request**
   - Go to GitHub: `newlens-uk/website-prod`
   - Click "New Pull Request"
   - Base: `main` (website-prod)
   - Compare: Cherry-pick commits from website-test
   - OR manually copy files if safer

10. **Review Changes**
    - Mat reviews the PR
    - Check diff carefully
    - Confirm this is exactly what was tested

11. **Merge to Production**
    - Click "Merge Pull Request"
    - Vercel auto-deploys to `prod.newlens.uk`

12. **Verify on Production**
    - Open `https://prod.newlens.uk`
    - Verify changes deployed correctly
    - Test critical functionality

---

## 🚨 Emergency Hotfix Workflow

For critical production bugs that can't wait:

1. **Create Restore Point** (MANDATORY)
   ```bash
   cd ~/.openclaw/workspace/website-prod
   git tag hotfix-$(date +%Y%m%d-%H%M%S)
   git push --tags
   ```

2. **Make Minimal Fix**
   - Only change what's broken
   - No feature additions

3. **Commit & Push**
   ```bash
   git commit -m "hotfix: brief description of fix"
   git push origin main
   ```

4. **Verify Immediately**
   - Check production site
   - Confirm fix works

5. **Backport to Test**
   - Apply same fix to `website-test`
   - Keep environments in sync

---

## 📊 Portal Update Workflow

Same as website, but with additional considerations:

### **Phase 1: Database Migrations**
If the change involves database schema:

1. **Test Migration on Portal-Test First**
   ```bash
   # In portal-test environment
   npm run migrate
   ```

2. **Verify Data Integrity**
   - Check Supabase dashboard
   - Test CRUD operations

3. **Document Migration**
   - Add migration notes to commit message
   - Update `docs/MIGRATIONS.md` if needed

### **Phase 2: Environment Variables**
If adding new env vars:

1. **Add to Portal-Test** (Vercel dashboard)
2. **Test thoroughly**
3. **Document in PR**
4. **Add to Portal-Prod** when merging

---

## ✅ Pre-Deployment Checklist

Before pushing ANY change to production:

- [ ] Change tested on `-test` environment
- [ ] Git tag restore point created
- [ ] All files committed (no uncommitted changes)
- [ ] Commit message is descriptive
- [ ] Mat has reviewed (for significant changes)
- [ ] No console errors in browser
- [ ] Mobile view tested (if applicable)
- [ ] Forms still submit correctly
- [ ] Navigation links work
- [ ] Images load correctly

---

## 🔍 Daily Health Checks

Newt runs these automatically each morning:

### **Website Health**
```bash
# Check both domains are responding
curl -s -o /dev/null -w "%{http_code}" https://test.newlens.uk
curl -s -o /dev/null -w "%{http_code}" https://prod.newlens.uk
```

### **Repo Sync Check**
```bash
# Verify test and prod aren't too far diverged
cd ~/.openclaw/workspace/
git -C website-test log --oneline -5
git -C website-prod log --oneline -5
```

### **Backup Verification**
```bash
# Confirm nightly backups are running
ls -lh ~/.openclaw/workspace/backups/ | tail -5
```

---

## 📝 Commit Message Standards

Use conventional commits format:

- `feat: Add new pricing page`
- `fix: Correct assessment form scroll behavior`
- `docs: Update REPO_GUIDE with new workflow`
- `style: Improve button hover states`
- `refactor: Reorganize services page structure`
- `hotfix: Fix broken contact form submission`

**Why this matters:** Clear commit messages make it easy to:
- Understand what changed
- Find when a bug was introduced
- Write release notes
- Roll back specific changes

---

## 🛡️ Newt's Responsibilities

### Before Making Changes
1. Read `REPO_GUIDE.md` and this file
2. Check `git status` and `pwd`
3. Verify I'm in the correct `-test` repo
4. Create restore point

### During Changes
1. Make focused, atomic commits
2. Test changes locally where possible
3. Document what and why in commit messages

### After Changes
1. Verify deployment on test domain
2. Report status to Mat
3. Wait for approval before production
4. Update memory logs

### Never Do
- Never edit production repos directly
- Never force push without explicit approval
- Never delete archives or backups
- Never skip the test environment
- Never assume - always verify

---

## 💬 Communication Protocol

### When Making Changes
- **Small changes:** "Updated X on test site, please review at test.newlens.uk"
- **Medium changes:** "Made changes to Y, here's what's different: [list]. Testing at test.newlens.uk"
- **Large changes:** "Major update to Z. Please review and approve before I push to production."

### When Things Go Wrong
- **Immediate notification:** "⚠️ Issue detected: [description]. Rolling back now."
- **Post-rollback:** "Restored to last known good state (tag: backup-YYYYMMDD-HHMMSS). Issue was: [root cause]"

---

## 📅 Weekly Review

Every Monday morning:
1. Review last week's changes
2. Check for divergence between test and prod
3. Verify backups are healthy
4. Update this workflow doc if needed

---

**This workflow was created on 2026-02-18 after learning expensive lessons about what happens when you don't have one.** 🛡️
