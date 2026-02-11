# TOMORROW MORNING - Step-by-Step Instructions

**Time needed**: 45 minutes  
**Goal**: Get n8n working and test forms

---

## STEP 1: Fix Cloudflare Tunnel (10 minutes)

### What's Wrong
n8n.newlens.uk returns 502 error because Cloudflare Tunnel is pointing to old Docker IP.

### Fix It

1. Open browser, go to: https://one.dash.cloudflare.com/
2. Login with your Cloudflare account
3. Click on "Zero Trust" in left sidebar
4. Click "Access" → "Tunnels"
5. You should see a tunnel with ID starting with `20df87a8...`
6. Click the three dots (•••) next to it
7. Click "Configure"
8. Under "Public Hostnames", find the row with `n8n.newlens.uk`
9. Click "Edit" on that row
10. In "Service" section, find the URL field (currently shows `http://172.18.0.2:5678`)
11. Change it to: `http://host.docker.internal:5678`
12. Click "Save hostname"
13. Wait 30 seconds
14. Test: Open new tab, go to https://n8n.newlens.uk/healthz
15. Should see: `{"status":"ok"}`

✅ **Success**: n8n is accessible!

---

## STEP 2: Activate n8n Workflows (20 minutes)

### Get Your HubSpot Token First

1. Go to: https://app.hubspot.com/
2. Login to your account
3. Click your account icon (top-right)
4. Click "Settings" (gear icon)
5. In left sidebar: "Integrations" → "Private Apps"
6. If you have a "newlens" app already:
   - Click on it
   - Click "Show token"
   - Copy the token
7. If not:
   - Click "Create private app"
   - Name: `newlens`
   - Scopes: Check these boxes:
     - `crm.objects.contacts.write`
     - `crm.objects.contacts.read`
     - `crm.schemas.contacts.read`
   - Click "Create app"
   - Copy the token that appears

### Now Activate Workflows

1. Go to: https://n8n.newlens.uk/
2. Login (should work now that tunnel is fixed)
3. You'll see a list of workflows on left

#### Workflow 1: AI Readiness Assessment

1. Click "Website: AI Readiness Assessment" in the list
2. You'll see a visual workflow with nodes
3. Find the "HubSpot" node (looks like a square with HubSpot logo)
4. Click on it
5. In the panel on right, find "Credentials"
6. Click the dropdown (probably shows "Select credential...")
7. Click "Create New"
8. Name: `HubSpot newlens`
9. Paste your token in the "Private App Token" field
10. Click "Save"
11. Click "Execute Node" button to test (should show success)
12. Close the node panel
13. At top-right of screen, find the toggle switch labeled "Active"
14. Click it to turn it ON (should turn green)

#### Workflow 2: Contact Enquiry

1. Click back arrow or "Workflows" at top-left
2. Click "Website: Contact Enquiry"
3. Find the HubSpot node, click it
4. In Credentials dropdown, select "HubSpot newlens" (the one you just made)
5. Close the node panel
6. Toggle "Active" at top-right

#### Workflow 3: Alpha Partner

1. Click back, click "Website: Alpha Partner"
2. Find HubSpot node, click it
3. Select "HubSpot newlens" credential
4. Close panel
5. Toggle "Active"

✅ **Success**: All three workflows show green "Active" indicator!

---

## STEP 3: Test Assessment Form (10 minutes)

1. Open: https://v2-site-sigma.vercel.app/assessment-new.html
2. Fill out the form with test data:
   - Name: Test User
   - Email: YOUR_EMAIL@gmail.com (use real email)
   - Company: Test Company
   - Click through all 8 steps
   - Submit at the end
3. Should see: Green success message "Application Received!"
4. Check your email inbox (within 1-2 minutes)
5. Go to HubSpot: https://app.hubspot.com/contacts/
6. Search for "Test User"
7. Click on the contact
8. Scroll down to "Contact properties"
9. Look for:
   - `assessment_score` (should have a number)
   - `assessment_tier` (should say "approved" or "review" or "waitlist")
   
✅ **Success**: Contact created with custom properties!

---

## STEP 4: Quick Test Other Forms (5 minutes)

### Contact Form
1. Go to: https://v2-site-sigma.vercel.app/contact.html
2. Fill out, submit
3. Check HubSpot for new contact

### Alpha Partner Form
1. Go to: https://v2-site-sigma.vercel.app/alpha-partner.html
2. Fill out 3 steps, submit
3. Check HubSpot for new contact

---

## IF SOMETHING GOES WRONG

### Cloudflare Tunnel Still 502
- Try: `http://localhost:5678` instead of `host.docker.internal:5678`
- Wait 2 minutes after saving
- Check Docker is running: SSH to EXE-004, run: `docker ps`

### Workflow Won't Activate
- Check the workflow execution log (click "Executions" in n8n)
- Look for error messages
- Common issue: HubSpot token wrong (check scopes)

### Form Submits But No Contact
- Open n8n, click "Executions" in left sidebar
- Look for the latest run (should be there even if failed)
- Click on it to see error message
- Usually: HubSpot token or property name issue

### Can't Login to n8n
- Check: Do you have n8n credentials set up?
- If not, SSH to EXE-004, check `/home/newt/n8n-docker/.env` file
- Should have `N8N_BASIC_AUTH_USER` and `N8N_BASIC_AUTH_PASSWORD`

---

## AFTER THIS IS DONE

**You're 80% to launch!** 🎉

Next steps (can do later today or Tuesday):
- Deploy portal to Vercel
- Set up backups
- Configure firewall

All documented in: `docs/LAUNCH_PLAN_COMPREHENSIVE.md`

---

## NEED HELP?

Message me (Newt) with:
- Screenshot of any error
- Which step you're stuck on
- What you see vs what you expect

I'll troubleshoot!
