# Thursday Quick Guide - 45 Minutes to Launch

**Print this. Tick boxes as you go.**

---

## ⏰ TIMING

- Start: Thursday morning (coffee break)
- Duration: 45 minutes
- Goal: Unblock everything

---

## STEP 1: FIX CLOUDFLARE TUNNEL (10 minutes)

### What You Need
- Cloudflare account login
- Access to: https://one.dash.cloudflare.com/

### Do This
1. ☐ Login to Cloudflare Dashboard
2. ☐ Click "Zero Trust" in left sidebar
3. ☐ Click "Access"
4. ☐ Click "Tunnels"
5. ☐ Find tunnel starting with `20df87a8...`
6. ☐ Click three dots (•••) → "Configure"
7. ☐ Under "Public Hostnames", find `n8n.newlens.uk`
8. ☐ Click "Edit"
9. ☐ Find the "Service" URL field
10. ☐ Change from: `http://172.18.0.2:5678`
11. ☐ Change to: `http://host.docker.internal:5678`
12. ☐ Click "Save hostname"
13. ☐ Wait 30 seconds
14. ☐ Open new tab: https://n8n.newlens.uk/healthz
15. ☐ Should see: `{"status":"ok"}`

### ✅ Success Test
```
Go to: https://n8n.newlens.uk/healthz
See: {"status":"ok"}
```

### ❌ If It Fails
Try: `http://localhost:5678` instead of `host.docker.internal:5678`

---

## STEP 2: GET HUBSPOT TOKEN (5 minutes)

### What You Need
- HubSpot account login
- Access to: https://app.hubspot.com/

### Do This
1. ☐ Login to HubSpot
2. ☐ Click your account icon (top-right)
3. ☐ Click "Settings" (gear icon)
4. ☐ Left sidebar: "Integrations" → "Private Apps"
5. ☐ **If you see "newlens" app**:
   - ☐ Click on it
   - ☐ Click "Show token"
   - ☐ Copy the token
   - ☐ **SKIP TO STEP 3**
6. ☐ **If no apps exist**:
   - ☐ Click "Create private app"
   - ☐ Name: `newlens`
   - ☐ Description: `Website forms integration`
   - ☐ Click "Scopes" tab
   - ☐ Check these 3 boxes:
     - `crm.objects.contacts.write`
     - `crm.objects.contacts.read`
     - `crm.schemas.contacts.read`
   - ☐ Click "Create app"
   - ☐ Copy the token that appears
   - ☐ Store it safely (you'll need it in next step)

### ✅ Success Test
You have a token that looks like: `pat-na1-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX`

---

## STEP 3: ACTIVATE N8N WORKFLOWS (15 minutes)

### What You Need
- n8n login credentials
- HubSpot token (from Step 2)
- Access to: https://n8n.newlens.uk/

### Do This - Workflow 1: Assessment

1. ☐ Go to https://n8n.newlens.uk/
2. ☐ Login (check password manager if needed)
3. ☐ You'll see list of workflows on left
4. ☐ Click "Website: AI Readiness Assessment"
5. ☐ You'll see visual workflow with boxes/nodes
6. ☐ Find the "HubSpot" node (orange box)
7. ☐ Click on it
8. ☐ On right panel, find "Credentials" dropdown
9. ☐ Click dropdown → "Create New"
10. ☐ Name: `HubSpot newlens`
11. ☐ Paste your token in "Private App Token" field
12. ☐ Click "Save"
13. ☐ Click "Execute Node" button (test it)
14. ☐ Should see green success checkmark
15. ☐ Close the node panel
16. ☐ Top-right corner: Find toggle switch "Active"
17. ☐ Click it to turn ON (should turn green)

### Do This - Workflow 2: Contact

1. ☐ Click back arrow or "Workflows" at top-left
2. ☐ Click "Website: Contact Enquiry"
3. ☐ Find HubSpot node, click it
4. ☐ Credentials dropdown → Select "HubSpot newlens" (the one you just made)
5. ☐ Close panel
6. ☐ Toggle "Active" at top-right

### Do This - Workflow 3: Alpha Partner

1. ☐ Click back, click "Website: Alpha Partner"
2. ☐ Find HubSpot node, click it
3. ☐ Select "HubSpot newlens" credential
4. ☐ Close panel
5. ☐ Toggle "Active"

### ✅ Success Test
Go to workflows list. All 3 should show:
- Green "Active" badge
- Green dot next to name

---

## STEP 4: TEST A FORM (10 minutes)

### What You Need
- Your real email address
- Access to: https://v2-site-sigma.vercel.app/

### Do This
1. ☐ Open: https://v2-site-sigma.vercel.app/assessment-new.html
2. ☐ Fill out form with these test values:
   - Name: `Test User`
   - Email: **YOUR REAL EMAIL**
   - Company: `Test Company`
   - Click through all 8 steps
   - Choose random answers
3. ☐ Click "Submit" on final step
4. ☐ Should see green success message: "Application Received!"
5. ☐ **Wait 1-2 minutes**
6. ☐ Check your email inbox
7. ☐ Should receive auto-reply from newlens
8. ☐ Go to HubSpot: https://app.hubspot.com/contacts/
9. ☐ Search for "Test User"
10. ☐ Click on the contact
11. ☐ Scroll down to "Contact properties"
12. ☐ Look for `assessment_score` (should have a number)
13. ☐ Look for `assessment_tier` (should say "approved" or "review" or "waitlist")

### ✅ Success Test
- Email received ✅
- Contact in HubSpot ✅
- assessment_score populated ✅
- assessment_tier populated ✅

### ❌ If It Fails
1. ☐ Go to n8n: https://n8n.newlens.uk/
2. ☐ Click "Executions" in left sidebar
3. ☐ Look for latest execution
4. ☐ Click on it
5. ☐ Read error message
6. ☐ Screenshot error
7. ☐ Message Newt with screenshot

---

## STEP 5: MESSAGE NEWT

### Copy This Template

```
All done! ✅

Cloudflare Tunnel: ✅ Working
HubSpot Token: ✅ Added
Workflows Active: ✅ All 3
Form Test: ✅ Passed

Ready for Friday-Sunday work while I'm away.
```

---

## OPTIONAL: DEPLOY PORTAL (15 minutes)

**Only if you have time. Not critical for website launch.**

### What You Need
- Vercel account login
- Access to: https://vercel.com/

### Do This
1. ☐ Login to Vercel
2. ☐ Click "Add New Project"
3. ☐ Click "Import from Git"
4. ☐ Select GitHub account
5. ☐ Find repository: `newlens-uk/newlens-portal`
6. ☐ Click "Import"
7. ☐ Framework: Should auto-detect "Next.js"
8. ☐ Click "Environment Variables"
9. ☐ Add these 4 variables:

```
NEXT_PUBLIC_SUPABASE_URL
Value: https://wlazofcwpjwyzdsusyxz.supabase.co

NEXT_PUBLIC_SUPABASE_ANON_KEY
Value: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6IndsYXpvZmN3cGp3eXpkc3VzeXh6Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mzk1NTY3NzgsImV4cCI6MjA1NTEzMjc3OH0.sb_publishable_U6OqwX2tRK2a9tIfnPSzrQ_xuomZ_Sc

SUPABASE_SERVICE_KEY
Value: [get from ~/.openclaw/workspace/newlens-portal/.env.local]

METRICS_API_KEY
Value: newlens_metrics_prod_2026
```

10. ☐ Click "Deploy"
11. ☐ Wait 2-3 minutes (watch build logs)
12. ☐ Should see "Congratulations!" message
13. ☐ Click "Visit" button
14. ☐ Should see portal login page
15. ☐ Go to Project Settings → Domains
16. ☐ Add custom domain: `portal.newlens.uk`
17. ☐ Follow DNS instructions (add CNAME)

### ✅ Success Test
- Build passed ✅
- Can visit portal URL ✅
- Login page loads ✅

---

## TROUBLESHOOTING

### Cloudflare Tunnel Returns 502
**Try**: Change URL to `http://localhost:5678` instead  
**Or**: SSH to EXE-004, run `docker ps` to check n8n is running

### Can't Login to n8n
**Check**: Do you have credentials?  
**Try**: Check `~/.openclaw/workspace/docs/N8N_DEPLOYMENT_EXE004.md` for password  
**Or**: SSH to EXE-004, check `/home/newt/n8n-docker/.env` file

### HubSpot Token Doesn't Work
**Check**: Did you enable all 3 scopes?  
**Try**: Regenerate token in HubSpot  
**Or**: Create a new private app

### Form Test Fails
**Check**: Are workflows Active (green)?  
**Check**: n8n Executions log for error  
**Try**: Execute workflow manually in n8n UI

### Portal Build Fails
**Check**: Did you add all 4 env variables?  
**Check**: Build logs for specific error  
**Message**: Newt with error screenshot

---

## HELPFUL LINKS

- Cloudflare Dashboard: https://one.dash.cloudflare.com/
- HubSpot Settings: https://app.hubspot.com/settings/
- n8n Dashboard: https://n8n.newlens.uk/
- Vercel Dashboard: https://vercel.com/dashboard
- Test Site: https://v2-site-sigma.vercel.app/

---

## TIME BREAKDOWN

- Cloudflare fix: 10 mins
- HubSpot token: 5 mins
- Activate workflows: 15 mins
- Test form: 10 mins
- Message Newt: 2 mins
- **Total: 42 minutes**

Optional:
- Deploy portal: 15 mins
- **Grand Total: 57 minutes**

---

## AFTER YOU'RE DONE

**Friday-Sunday**: Enjoy your break with Karen! 🌹  
**Monday**: 30 minutes to make site live  
**Tuesday**: Show off at networking event! 🎉

Newt will handle everything over the weekend.

---

**Ready? Let's do this! You've got 45 minutes to unblock a launch. 🚀**

*Guide created: 2026-02-11 20:35 GMT*
