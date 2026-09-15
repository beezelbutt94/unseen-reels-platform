# Setup Guide: Unseen Reels Platform

Complete setup instructions for local development and production deployment.

## Phase 1: Initial Setup

### 1. GitHub Repository

1. Create a new public repository on GitHub:
   - Go to https://github.com/new
   - Repository name: `unseen-reels-platform`
   - Description: "B2B faceless video production platform"
   - Public (to match other repos)
   - Initialize with no template

2. Push this scaffolded project:
   ```bash
   cd /path/to/unseen-reels-platform
   git init
   git add .
   git commit -m "feat: initial project scaffolding

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>"
   git branch -M main
   git remote add origin https://github.com/beezelbutt94/unseen-reels-platform.git
   git push -u origin main
   ```

### 2. Supabase Setup

1. Go to https://supabase.com and sign in
2. Create a new project:
   - Name: "unseen-reels"
   - Password: Generate strong password
   - Region: Choose closest to your users (EU recommended for €)

3. Once created, run the database schema:
   - Go to SQL Editor
   - Create new query
   - Copy and paste contents of `database/schema.sql`
   - Run the query
   - Verify all tables created successfully

4. Copy your credentials:
   - Go to Project Settings → API
   - Copy `Project URL` → `SUPABASE_URL`
   - Copy `anon key` → `SUPABASE_ANON_KEY` (frontend + public)
   - Copy `service_role key` → `SUPABASE_SERVICE_ROLE_KEY` (backend only)

### 3. API Keys & Credentials

Collect the following (you can add them to .env files later):

#### Claude API (Script Generation)
1. Go to https://console.anthropic.com
2. Go to API Keys
3. Create new API key
4. Copy the key → `CLAUDE_API_KEY`

#### Stripe (Payment Processing)
1. Go to https://dashboard.stripe.com
2. Get test keys from Settings → API Keys
3. Copy `Secret key` → `STRIPE_SECRET_KEY`
4. Copy `Publishable key` → `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`
5. Copy webhook signing secret → `STRIPE_WEBHOOK_SECRET`

#### D-ID or Runway (Video Production)
Choose ONE:

**D-ID** (Recommended for avatars):
1. Go to https://www.d-id.com
2. Sign up for API access
3. Get API key → `DID_API_KEY`

**Runway** (Alternative for effects):
1. Go to https://app.runwayml.com
2. Get API key → `RUNWAY_API_KEY`

#### Cloudinary (Asset Management)
1. Go to https://cloudinary.com
2. Get API credentials from Settings
3. Copy `Cloud Name` → `CLOUDINARY_CLOUD_NAME`
4. Copy `API Key` → `CLOUDINARY_API_KEY`
5. Copy `API Secret` → `CLOUDINARY_API_SECRET`

### 4. Local Development Setup

1. Clone and setup:
   ```bash
   git clone https://github.com/beezelbutt94/unseen-reels-platform.git
   cd unseen-reels-platform
   ```

2. Setup environment files:
   ```bash
   # Backend
   cp backend/.env.example backend/.env
   # Edit backend/.env with your API keys

   # Frontend
   cp frontend/.env.example frontend/.env.local
   # Edit frontend/.env.local with Supabase keys
   ```

3. Install dependencies:
   ```bash
   # Backend
   cd backend
   npm install
   cd ..

   # Frontend
   cd frontend
   npm install
   cd ..
   ```

4. Start development:
   ```bash
   # Terminal 1: Backend
   cd backend && npm run dev
   # Should show: ✓ Unseen Reels API running on port 3001

   # Terminal 2: Frontend
   cd frontend && npm run dev
   # Should show: http://localhost:3000
   ```

5. Test the setup:
   ```bash
   # Check backend health
   curl http://localhost:3001/api/health
   # Should return: {"status":"ok","service":"unseen-reels-backend",...}

   # Frontend loads on browser
   # Visit http://localhost:3000
   ```

## Phase 2: Production Deployment

### Railway Backend Setup

1. Go to https://railway.app
2. Create new project → Deploy from GitHub
3. Select repository: `unseen-reels-platform`
4. Auto-detect: Backend (Node.js)
5. Configure environment:
   - Add variables from `backend/.env` to Railway dashboard
   - Click "Generate Domain" for API URL

6. GitHub Actions integration:
   - Go to GitHub Settings → Actions → General
   - Create required secrets (see below)

### Vercel Frontend Setup

1. Go to https://vercel.com
2. Import project from GitHub
3. Select repository: `unseen-reels-platform`
4. Configure:
   - Framework: Next.js
   - Root Directory: `./frontend`
   - Environment variables: Add from `frontend/.env.example`
   - Click Deploy

### GitHub Secrets (for CI/CD)

Go to GitHub repo → Settings → Secrets and variables → Actions

Add these secrets:

```
# Supabase
SUPABASE_URL
SUPABASE_ANON_KEY
SUPABASE_SERVICE_ROLE_KEY

# APIs
CLAUDE_API_KEY
STRIPE_SECRET_KEY
STRIPE_WEBHOOK_SECRET
DID_API_KEY
RUNWAY_API_KEY

# Cloudinary
CLOUDINARY_CLOUD_NAME
CLOUDINARY_API_KEY
CLOUDINARY_API_SECRET

# Deployment
RAILWAY_API_TOKEN           (from railway.app account settings)
RAILWAY_PROJECT_ID          (from Railway project)
RAILWAY_BACKEND_SERVICE_ID  (from Railway service)
VERCEL_TOKEN                (from Vercel account settings)
VERCEL_ORG_ID               (your Vercel org)
VERCEL_PROJECT_ID_FRONTEND  (from Vercel project)
```

## Phase 3: Monitoring & Observability

### Sentry Setup

1. Go to https://sentry.io
2. Create new organization
3. Create project for "Node" (backend) + "React" (frontend)
4. Get DSN values
5. Add to environment variables:
   - Backend: `SENTRY_DSN`
   - Frontend: `NEXT_PUBLIC_SENTRY_DSN`

## Verification Checklist

- [ ] GitHub repository created and scaffolding pushed
- [ ] Supabase project created and schema deployed
- [ ] Database tables created with proper indexes
- [ ] API keys collected for all services
- [ ] Backend .env file created with all keys
- [ ] Frontend .env.local file created
- [ ] `npm install` successful in both directories
- [ ] Backend health check responding
- [ ] Frontend loads on localhost:3000
- [ ] GitHub Actions workflows visible in Actions tab
- [ ] Railway project connected
- [ ] Vercel project connected
- [ ] GitHub secrets configured (all 13+ values)
- [ ] First commit and push verified with CI/CD running

## Common Issues

### Backend won't start
- Check Node.js version: `node -v` (should be 20.x)
- Check SUPABASE_URL format (should include protocol)
- Verify .env file has all required variables

### Database schema not applying
- Check SQL syntax in schema.sql
- Verify Supabase project is created
- Try creating tables manually first

### Frontend not connecting to API
- Check NEXT_PUBLIC_API_URL points to backend
- Verify backend is running on :3001
- Check browser console for CORS errors

### CI/CD not triggering
- Verify GitHub Actions enabled in repo settings
- Check secrets are set correctly (typos matter)
- Ensure workflows are in `.github/workflows/`

## Next Steps

1. After local setup verification, proceed to Phase 2: Database Schema
2. Create first test user via Supabase Auth
3. Test API endpoints with curl or Postman
4. Deploy to staging on Railway
5. Deploy frontend to Vercel preview

See README.md for architecture details and API endpoint documentation.
