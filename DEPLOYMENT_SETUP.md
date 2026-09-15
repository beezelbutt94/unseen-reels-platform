# Deployment Setup: Railway + Vercel + GitHub Secrets

Complete guide to deploy Unseen Reels production infrastructure.

## Step 1: GitHub Secrets Configuration

Go to: https://github.com/beezelbutt94/unseen-reels-platform/settings/secrets/actions

Add these secrets (click "New repository secret" for each):

### Supabase Credentials
```
SUPABASE_URL = https://dcesehxmssqsszzasott.supabase.co
SUPABASE_ANON_KEY = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImRjZXNlaHhtc3Nxc3N6emFzb3R0Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODkzOTY1OTIsImV4cCI6MjEwNDk3MjU5Mn0.KPHFvdJTDJlFLSKPulrd3EHyqeJX-x-vMhiqdxbLVTo
SUPABASE_SERVICE_ROLE_KEY = [paste your service role key]
```

### Railway Credentials
```
RAILWAY_API_TOKEN = [get from railway.app/account → API Tokens]
RAILWAY_PROJECT_ID = 45fe9746-7751-414d-afbe-d831084d8bfc
RAILWAY_BACKEND_SERVICE_ID = [will get after Railway setup]
```

### Vercel Credentials
```
VERCEL_TOKEN = [your Vercel API token from vercel.com/account → settings]
VERCEL_ORG_ID = [get from vercel.com/account → settings]
VERCEL_PROJECT_ID_FRONTEND = [will get after Vercel setup]
```

### Optional (Add Later if Needed)
```
CLAUDE_API_KEY = [from console.anthropic.com]
STRIPE_SECRET_KEY = [from Stripe dashboard]
STRIPE_WEBHOOK_SECRET = [from Stripe webhooks]
DID_API_KEY = [from D-ID account]
RUNWAY_API_KEY = [from Runway account]
CLOUDINARY_CLOUD_NAME = [from Cloudinary]
CLOUDINARY_API_KEY = [from Cloudinary]
CLOUDINARY_API_SECRET = [from Cloudinary]
SENTRY_DSN = [from Sentry project]
```

**You have 7 secrets ready now** (Supabase + Railway + Vercel)

---

## Step 2: Railway Backend Setup

1. Go to https://railway.app/dashboard
2. Create new project
3. Click "Deploy from GitHub repo"
4. Select: `beezelbutt94/unseen-reels-platform`
5. Select branch: `main`
6. Click "Deploy"

### Configure Backend Service

Once Railway creates the service:

1. Click on the backend service
2. Go to **Settings** → **Environment**
3. Add variables:
   ```
   PORT=3001
   NODE_ENV=production
   SUPABASE_URL=[from secrets]
   SUPABASE_SERVICE_ROLE_KEY=[from secrets]
   CLAUDE_API_KEY=[add later if needed]
   DID_API_KEY=[add later if needed]
   ```

4. Go to **Deployments** tab
5. Wait for green checkmark (deployment complete)
6. Click on deployment → view logs to verify success

### Get Railway Service ID
1. In Railway, go to Project Settings
2. Copy your `Project ID` → save as `RAILWAY_PROJECT_ID` in GitHub (already done)
3. Go to backend service → copy `Service ID` → save as `RAILWAY_BACKEND_SERVICE_ID` in GitHub secrets

### Get Railway API Token
1. Go to https://railway.app/account → API Tokens
2. Create new token
3. Copy and save as `RAILWAY_API_TOKEN` in GitHub secrets

---

## Step 3: Vercel Frontend Setup

1. Go to https://vercel.com/dashboard
2. Click "Add new..." → "Project"
3. Import Git Repository
4. Search and select: `unseen-reels-platform`
5. Click "Import"

### Configure Frontend Project

In Vercel project settings:

1. **Root Directory**: Set to `./frontend`
2. **Framework Preset**: Next.js
3. **Environment Variables**: Add
   ```
   NEXT_PUBLIC_SUPABASE_URL=https://dcesehxmssqsszzasott.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=[from secrets]
   NEXT_PUBLIC_API_URL=https://[railway-domain].railway.app
   NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=[add later]
   NEXT_PUBLIC_SENTRY_DSN=[add later]
   NEXT_PUBLIC_ENV=production
   ```
4. Click "Deploy"

### Get Vercel Project ID
1. In Vercel, go to Project Settings
2. Copy `Project ID`
3. Save as `VERCEL_PROJECT_ID_FRONTEND` in GitHub secrets

### Get Vercel Org ID
1. Go to https://vercel.com/account/settings
2. Copy `Team ID` (this is your Org ID)
3. Save as `VERCEL_ORG_ID` in GitHub secrets

---

## Step 4: Verify Deployments

### Check Railway
- Go to Railway dashboard
- Click backend service
- Verify status is "Deployed"
- Click "Generate Domain" to get public URL
- Test: `curl https://[railway-domain].railway.app/api/health`
- Should return: `{"status":"ok","service":"unseen-reels-backend"}`

### Check Vercel
- Go to Vercel dashboard
- Click frontend project
- Verify status shows "Ready"
- Visit the deployment URL
- Should load React app on `localhost:3000` equivalent

### Check GitHub Actions
1. Go to: https://github.com/beezelbutt94/unseen-reels-platform/actions
2. Should see successful workflows:
   - ✓ Backend CI (passed)
   - ✓ Frontend CI (passed)
   - ✓ Deploy (passed - if main branch)

---

## Step 5: Update GitHub Secrets (Final)

Once Railway and Vercel are deployed, add these missing secrets:

```
RAILWAY_BACKEND_SERVICE_ID = [from Railway service]
VERCEL_PROJECT_ID_FRONTEND = [from Vercel project]
VERCEL_ORG_ID = [from Vercel account]
```

---

## Step 6: Supabase Database Schema (Final Check)

Make sure schema.sql was deployed:

1. Go to https://supabase.com → your project
2. Click **Table Editor**
3. Verify these 8 tables exist:
   - ✓ users
   - ✓ subscriptions
   - ✓ projects
   - ✓ briefs
   - ✓ scripts
   - ✓ videos
   - ✓ invoices
   - ✓ workflow_logs

If NOT deployed yet:
1. Go to **SQL Editor**
2. Create new query
3. Copy entire `database/schema.sql` file
4. Paste and Run
5. Verify success

---

## Step 7: Test End-to-End

### Backend API Test
```bash
# From your terminal
curl https://[railway-domain].railway.app/api/health

# Expected response:
# {"status":"ok","service":"unseen-reels-backend","timestamp":"...","version":"0.1.0"}
```

### Frontend Load Test
- Visit Vercel deployment URL in browser
- Should load without errors
- Check browser console for any CORS/API errors

### Database Connection Test
- In Vercel/Railway logs, should see Supabase client initialized
- No "connection refused" errors

---

## Troubleshooting

### Railway deployment fails
- Check logs: Railway → backend service → Deployments → view logs
- Common issues:
  - `PORT not set` → Add PORT=3001 to environment
  - `SUPABASE_URL missing` → Add to Railway environment variables
  - `npm install failed` → Check backend/package.json syntax

### Vercel deployment fails
- Check logs: Vercel → project → Deployments → logs
- Common issues:
  - `Root directory not found` → Set root to `./frontend`
  - `Build failed` → Check frontend/package.json dependencies
  - `API_URL incorrect` → Should point to Railway domain

### GitHub Actions not triggering
- Check `.github/workflows/` files in repo
- Workflows trigger on `git push main`
- Verify secrets are set (Actions can't run without them)

### Secrets not available to workflows
- Go to Settings → Secrets → Actions
- Verify all 7 required secrets are present
- Check secret names match exactly (case-sensitive)
- Workflows take ~1 min to pick up new secrets

---

## Summary: What's Now Live

| Component | Status | URL |
|-----------|--------|-----|
| Backend API | ✓ Deployed | `https://[railway-domain].railway.app` |
| Frontend App | ✓ Deployed | Vercel domain |
| Database | ✓ Deployed | Supabase cloud |
| CI/CD | ✓ Enabled | GitHub Actions |
| Error Tracking | ⏳ Ready | Sentry (setup optional) |

---

## Next Phase: Development

Once deployments are verified:

1. **Local Development**
   ```bash
   cd backend && npm install && npm run dev
   cd frontend && npm install && npm run dev
   ```

2. **Push Changes**
   - Make code changes
   - Commit: `git commit -m "..."`
   - Push: `git push origin main`
   - GitHub Actions runs automatically
   - Railway + Vercel deploy automatically

3. **Monitor**
   - Railway logs: Backend activity
   - Vercel logs: Frontend activity
   - Sentry: Errors in production
   - GitHub Actions: Build/test results

---

Generated by Claude Haiku 4.5
