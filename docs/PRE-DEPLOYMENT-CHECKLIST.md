# Pre-Deployment Checklist

**READ THIS BEFORE EVERY DEPLOYMENT**

## Step 1: Confirm Location
```bash
pwd
# Expected: /home/newt/.openclaw/workspace/newlens-v2-preview
```

❌ **STOP** if you're anywhere else!

## Step 2: Check Repository
```bash
git remote -v
# Expected: newlens-uk/newlens-v2-preview
```

❌ **STOP** if it shows `newlens-v2` (wrong repo!)

## Step 3: Pull Latest
```bash
git pull origin main
```

❌ **STOP** if there are conflicts - resolve them first!

## Step 4: Check What Changed
```bash
git status
git diff
```

Review your changes carefully.

## Step 5: Stage Specific Files
```bash
# Never use: git add .
# Always use: git add <specific-file>
git add index.html
git add alpha-partner.html
git add assessment-new.html
```

## Step 6: Commit
```bash
git commit -m "Clear description of what changed"
```

## Step 7: Push
```bash
git push origin main
```

## Step 8: Verify (30-45 seconds later)
```bash
sleep 40
curl -s "https://v2-site-sigma.vercel.app/" | head -20
```

Look for your changes in the output.

---

## Common Mistakes to Avoid

1. ❌ Working in `newlens-v2` instead of `newlens-v2-preview`
2. ❌ Using `git add .` instead of specific files
3. ❌ Force pushing without documenting why
4. ❌ Not pulling before making changes
5. ❌ Not verifying deployment after push

---

**If something goes wrong**: Read `docs/DEPLOYMENT_PROTOCOL.md` section "If Something Goes Wrong"
