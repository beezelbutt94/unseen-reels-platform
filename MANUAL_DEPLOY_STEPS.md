# Manual Deployment Steps (while connector auth is being sorted)

> **Where to find the actual key values**: your real Supabase keys are already saved locally in `backend/.env` and `frontend/.env.local` (both gitignored — never committed). Open those files to copy values when a step below says "paste your key". This doc intentionally does not contain real secrets since this repo is public.

## 1. GitHub Secrets (5 min)

Go to: https://github.com/beezelbutt94/unseen-reels-platform/settings/secrets/actions

Click "New repository secret" for each of these 5. Copy each value from `backend/.env`:

```
SUPABASE_URL              → copy from backend/.env
SUPABASE_ANON_KEY         → copy from backend/.env
SUPABASE_SERVICE_ROLE_KEY → copy from backend/.env
SUPABASE_PUBLISHABLE_KEY  → copy from backend/.env
SUPABASE_SECRET_KEY       → copy from backend/.env
```

(RAILWAY_* and VERCEL_* secrets come after steps 2 and 3 below, once those services exist and you have their IDs.)

---

## 2. Railway Backend Deploy (5-10 min)

1. Go to https://railway.app/dashboard (make sure you're logged into the account that owns your existing projects — not a fresh/different one)
2. Click **"New Project"** → **"Deploy from GitHub repo"**
3. Authorize Railway's GitHub App if prompted, then select **`beezelbutt94/unseen-reels-platform`**
4. Railway will try to auto-detect — when it creates the service, go into **Settings**:
   - **Root Directory**: `backend`
   - **Start Command**: leave as-is (Procfile handles it: `npm run build && npm start`)
5. Go to **Variables** tab, add (values from your local `backend/.env`):
   ```
   SUPABASE_URL=<copy from backend/.env>
   SUPABASE_SERVICE_ROLE_KEY=<copy from backend/.env>
   PORT=3001
   NODE_ENV=production
   FRONTEND_URL=https://placeholder.vercel.app
   ```
   (You'll update FRONTEND_URL once Vercel is deployed in step 3)
6. Railway auto-deploys on save. Watch the **Deployments** tab for a green checkmark.
7. Once deployed, click **"Generate Domain"** (Settings → Networking) to get a public URL like `unseen-reels-backend-production.up.railway.app`
8. **Test it**: visit `https://[your-railway-domain]/api/health` in a browser — should show `{"status":"ok",...}`

**Save these for later:**
- Railway Project ID (Project Settings → General)
- Railway Backend Service ID (click the service → Settings → General)
- Railway API Token (railway.app/account/tokens → create new)

---

## 3. Vercel Frontend Deploy (5 min)

1. Go to https://vercel.com/dashboard (again — correct account, the one that owns viraltrending.online etc.)
2. Click **"Add New..."** → **"Project"**
3. **Import Git Repository** → select `beezelbutt94/unseen-reels-platform`
4. Configure:
   - **Root Directory**: click "Edit" → set to `frontend`
   - **Framework Preset**: Next.js (should auto-detect)
5. **Environment Variables** — add (values from your local `frontend/.env.local`):
   ```
   NEXT_PUBLIC_SUPABASE_URL = <copy from frontend/.env.local>
   NEXT_PUBLIC_SUPABASE_ANON_KEY = <copy from frontend/.env.local>
   NEXT_PUBLIC_API_URL = https://[your-railway-domain-from-step-2]
   NEXT_PUBLIC_ENV = production
   ```
6. Click **Deploy**. Takes ~2 min.
7. Once live, go back to **Railway** and update `FRONTEND_URL` variable to the real Vercel URL it gives you (e.g. `https://unseen-reels-platform.vercel.app`) — this fixes CORS between frontend and backend.

**Save these for later:**
- Vercel Project ID (Project Settings → General)
- Vercel Org/Team ID (Account Settings, or Project Settings → General shows it too)

---

## 4. (Optional now) Connect viraltrending.online

Once step 3 is live:
1. In Vercel project → **Settings → Domains** → add `viraltrending.online`
2. Vercel shows you a DNS record (usually an `A` record to `76.76.21.21` or a CNAME)
3. Go to GoDaddy → **My Products** → `viraltrending.online` → **DNS** → add that record
4. Propagation takes a few minutes to a few hours

*(Remember the brand-mismatch flag from earlier — domain says "Viral Trending", product is "Unseen Reels". Your call, just make sure it's intentional before this goes out to prospects.)*

---

## 5. Back-fill remaining GitHub Secrets

Once steps 2-3 are done, add these final 6 secrets (Settings → Secrets → Actions):

```
RAILWAY_API_TOKEN = [from railway.app/account/tokens]
RAILWAY_PROJECT_ID = [from Railway project settings]
RAILWAY_BACKEND_SERVICE_ID = [from Railway service settings]
VERCEL_TOKEN = [from vercel.com/account/tokens]
VERCEL_ORG_ID = [from Vercel project/account settings]
VERCEL_PROJECT_ID_FRONTEND = [from Vercel project settings]
```

This makes the `deploy.yml` GitHub Action work for future pushes (auto-deploy on merge to main).

---

## Verification Checklist

- [ ] 5 Supabase secrets added to GitHub
- [ ] Railway backend deployed, `/api/health` returns 200
- [ ] Vercel frontend deployed, loads in browser
- [ ] Railway `FRONTEND_URL` updated to real Vercel URL
- [ ] (optional) viraltrending.online DNS pointed at Vercel
- [ ] Remaining 6 GitHub secrets added for CI/CD auto-deploy

Reply with results/URLs once done and I'll verify everything end-to-end.
