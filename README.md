# Unseen Reels Platform

B2B faceless video production service for content creators. A complete platform for managing video production workflows from lead capture to delivery.

**Status**: Phase 1 - Infrastructure & Database Setup

## Project Structure

```
unseen-reels-platform/
├── backend/              # Node.js + Express API
│   ├── src/
│   │   ├── index.ts     # Entry point
│   │   ├── routes/      # API route handlers
│   │   ├── middleware/  # Express middleware
│   │   ├── services/    # Business logic
│   │   └── config/      # Configuration
│   ├── tests/           # Test files
│   └── package.json
├── frontend/            # React + Next.js app
│   ├── app/            # Next.js app directory
│   ├── components/     # React components
│   ├── pages/          # API routes
│   └── package.json
├── database/           # Database migrations
│   ├── schema.sql      # Initial schema
│   └── migrations/     # Migration files
└── .github/            # GitHub Actions CI/CD
```

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Database | Supabase (PostgreSQL) |
| Backend | Node.js + Express + TypeScript |
| Frontend | React + Next.js + Tailwind CSS |
| Video Production | D-ID or Runway ML API |
| AI | Claude API (Anthropic) |
| Payments | Stripe |
| Hosting | Railway (backend) + Vercel (frontend) |
| Error Tracking | Sentry |
| Asset Management | Cloudinary |

## Quick Start

### Prerequisites
- Node.js 20.x
- npm or yarn
- PostgreSQL (via Supabase)
- API Keys:
  - Supabase URL & keys
  - Claude API key
  - Stripe test keys
  - D-ID or Runway API credentials

### Development Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/beezelbutt94/unseen-reels-platform.git
   cd unseen-reels-platform
   ```

2. **Setup environment variables**
   ```bash
   # Backend
   cp backend/.env.example backend/.env
   # Edit backend/.env with your API keys

   # Frontend
   cp frontend/.env.example frontend/.env.local
   # Edit frontend/.env.local with your Supabase keys
   ```

3. **Install dependencies**
   ```bash
   # Backend
   cd backend && npm install

   # Frontend
   cd frontend && npm install
   ```

4. **Setup database**
   - Create a new Supabase project
   - Run the schema SQL from `database/schema.sql` in Supabase SQL editor

5. **Start development servers**
   ```bash
   # Terminal 1: Backend (runs on :3001)
   cd backend && npm run dev

   # Terminal 2: Frontend (runs on :3000)
   cd frontend && npm run dev
   ```

6. **Access the app**
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:3001
   - API Health: http://localhost:3001/api/health

## Architecture

### Database Layer
- Supabase PostgreSQL with Row-Level Security (RLS)
- Tables: users, projects, briefs, scripts, videos, subscriptions, invoices, workflow_logs
- Automated timestamping triggers

### API Layer
- Express.js RESTful API
- Supabase Auth integration
- JWT token validation
- CORS configuration for frontend

### Frontend Layer
- Next.js 14 with App Router
- TypeScript for type safety
- Tailwind CSS for styling
- Real-time updates via Supabase Realtime

### Production Workflow
```
Lead (from landing page)
  ↓
Brief Submission (client portal)
  ↓
Script Generation (Claude API)
  ↓
Video Production (D-ID/Runway)
  ↓
QA Checks (automated)
  ↓
Delivery (client download)
  ↓
Invoice & Archive
```

## API Endpoints (Phase 1)

### Health
- `GET /health` - Server health check
- `GET /api/health` - API health check

### Auth (Phase 2)
- `POST /api/auth/signup` - User registration
- `POST /api/auth/login` - User login
- `POST /api/auth/logout` - User logout

### Projects (Phase 2)
- `GET /api/projects` - List user projects
- `POST /api/projects` - Create project
- `GET /api/projects/:id` - Get project details
- `PATCH /api/projects/:id` - Update project

### Scripts (Phase 4)
- `POST /api/scripts/generate` - Generate script from brief
- `GET /api/scripts/:id` - Get script
- `PATCH /api/scripts/:id/approve` - Approve script

### Videos (Phase 4)
- `POST /api/videos/generate` - Trigger video generation
- `GET /api/videos/:id` - Get video status
- `GET /api/videos/:id/download` - Get download link

## CI/CD Pipeline

### GitHub Actions Workflows
- **backend-ci.yml**: Runs tests, linting, and builds on backend changes
- **frontend-ci.yml**: Runs type-check, linting, and builds on frontend changes
- **deploy.yml**: Deploys to Railway (backend) and Vercel (frontend) on main branch merge

### Required Secrets (GitHub Settings)
```
SUPABASE_URL
SUPABASE_ANON_KEY
SUPABASE_SERVICE_ROLE_KEY
CLAUDE_API_KEY
STRIPE_SECRET_KEY
STRIPE_WEBHOOK_SECRET
DID_API_KEY
RAILWAY_API_TOKEN
RAILWAY_PROJECT_ID
RAILWAY_BACKEND_SERVICE_ID
VERCEL_TOKEN
VERCEL_ORG_ID
VERCEL_PROJECT_ID_FRONTEND
```

## Development Workflow

### Running Tests
```bash
# Backend
cd backend && npm test

# Frontend
cd frontend && npm test
```

### Code Quality
```bash
# Backend
cd backend && npm run lint
cd backend && npm run type-check

# Frontend
cd frontend && npm run lint
cd frontend && npm run type-check
```

### Building for Production
```bash
# Backend
cd backend && npm run build

# Frontend
cd frontend && npm run build
```

## Deployment

### Backend (Railway)
- Automatic deployment on main branch via GitHub Actions
- Environment variables configured in Railway dashboard
- Worker process for video generation queue

### Frontend (Vercel)
- Automatic deployment on main branch via GitHub Actions
- Environment variables configured in Vercel dashboard
- CDN-distributed Next.js app

## Phases & Milestones

### ✓ Phase 1: Infrastructure & Database (Week 1-2)
- [x] Project scaffolding
- [ ] Database schema creation
- [ ] Basic API endpoints
- [ ] CI/CD pipeline

### Phase 2: Client Portal (Week 3-4)
- Brief submission form
- Project dashboard
- Progress tracking
- Invoice viewing

### Phase 3: Admin Dashboard (Week 5-6)
- Lead management
- Project queue
- Script review interface
- Team assignment

### Phase 4: AI & Video Production (Week 7-9)
- Claude script generation
- D-ID/Runway video production
- QA pipeline
- Delivery system

### Phase 5: Payment & Subscription (Week 10-11)
- Stripe integration
- Subscription management
- Usage limits per tier
- Invoice generation

### Phase 6: Monitoring & Polish (Week 12)
- Sentry error tracking
- Performance optimization
- Security hardening
- End-to-end testing

## Support

For issues or questions:
1. Check GitHub Issues
2. Review database schema documentation
3. Check API health endpoints

## License

Proprietary - Unseen Reels 2024-2026
