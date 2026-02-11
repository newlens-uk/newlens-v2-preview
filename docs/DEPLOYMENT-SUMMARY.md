# Website Deployment - Quick Summary

## The One Truth

**Work here and ONLY here:**
```
cd ~/.openclaw/workspace/newlens-v2-preview
```

**Push here and ONLY here:**
```
git push origin main
→ Auto-deploys to: https://v2-site-sigma.vercel.app/
```

## Before Every Deployment

1. **Read**: `docs/PRE-DEPLOYMENT-CHECKLIST.md`
2. **Follow**: The 8-step checklist
3. **Verify**: Changes are live after 40 seconds

## If You're Confused

**Read**: `docs/DEPLOYMENT_PROTOCOL.md` (the full manual)

## Key Files

| File | Purpose |
|------|---------|
| `index.html` | Homepage |
| `alpha-partner.html` | Alpha Partner form |
| `assessment-new.html` | Assessment form |
| `contact.html` | Contact page |
| `privacy.html` | Privacy policy |

## Emergency Contact

If something breaks:
1. Don't panic
2. Read `DEPLOYMENT_PROTOCOL.md` → "If Something Goes Wrong"
3. Emergency reset: `git fetch origin && git reset --hard origin/main`

---

**Created**: 2026-02-11  
**Purpose**: Prevent the chaos that happened today
