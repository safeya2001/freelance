# Freelance.jo — Bilingual Jordanian Freelancing Marketplace

A full-stack, production-ready freelancing marketplace for the Jordanian market. Supports Arabic (RTL) and English (LTR), JOD currency, and two workflow types: Gig Marketplace (Fiverr-style) and Project Marketplace (Upwork-style).

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14 + TypeScript + TailwindCSS + next-i18next |
| Backend | NestJS + TypeScript + postgres.js (raw SQL) |
| Database | PostgreSQL 16 |
| Cache / Sessions | Redis 7 |
| Real-time | Socket.IO (WebSocket) |
| Payments | Stripe Checkout + Stripe Webhooks |
| PDF Generation | Puppeteer (Chromium) |
| Auth | JWT (access + refresh) + Google OAuth 2.0 |
| Reverse Proxy | Nginx |
| Deployment | Docker Compose |

---

## Quick Start

### 1. Clone & configure

```bash
git clone <repo>
cd freelance

# Copy and fill in environment variables
cp .env.example .env
```

### 2. Fill in `.env`

```
# Required secrets
JWT_SECRET=<random 64 chars>
JWT_REFRESH_SECRET=<random 64 chars>
DB_PASSWORD=<strong password>
REDIS_PASSWORD=<strong password>

# Stripe (test keys for development)
STRIPE_SECRET_KEY=sk_test_...
STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Google OAuth
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...

# SMTP (e.g. Brevo / Mailtrap for dev)
SMTP_HOST=smtp.brevo.com
SMTP_PORT=587
SMTP_USER=your@email.com
SMTP_PASS=your_smtp_pass
```

### 3. Start everything

```bash
docker-compose up --build -d
```

That's it. The app will be available at **http://localhost**.

---

## Service URLs

| Service | URL |
|---|---|
| Frontend (Next.js) | http://localhost |
| Backend API | http://localhost/api |
| Socket.IO | ws://localhost/socket.io |
| Uploads | http://localhost/uploads |
| Backend direct | http://localhost:3001 |
| Frontend direct | http://localhost:3000 |
| PostgreSQL | localhost:5432 |
| Redis | localhost:6379 |

---

## Default Admin Credentials

After first run, a super admin user is seeded:

| Field | Value |
|---|---|
| Email | admin@freelance.jo |
| Password | Admin@123! |

**Change this immediately in production.**

---

## Directory Structure

```
freelance/
├── backend/                  # NestJS API
│   ├── src/
│   │   ├── database/         # migrations + DB module
│   │   └── modules/
│   │       ├── auth/         # JWT + Google OAuth
│   │       ├── users/        # profiles, skills
│   │       ├── gigs/         # gig CRUD + packages
│   │       ├── projects/     # project posting
│   │       ├── proposals/    # freelancer proposals
│   │       ├── contracts/    # contract management
│   │       ├── milestones/   # milestone tracking
│   │       ├── orders/       # gig orders
│   │       ├── payments/     # Stripe + webhooks
│   │       ├── escrow/       # escrow logic
│   │       ├── chat/         # Socket.IO gateway
│   │       ├── documents/    # Puppeteer PDF
│   │       ├── withdrawals/  # withdrawal requests
│   │       ├── notifications/# in-app notifications
│   │       ├── reviews/      # ratings & reviews
│   │       ├── disputes/     # dispute system
│   │       ├── tickets/      # support tickets
│   │       ├── search/       # full-text search
│   │       ├── uploads/      # file uploads
│   │       └── admin/        # admin endpoints
│   └── Dockerfile
├── frontend/                 # Next.js app
│   ├── public/locales/       # en + ar translations
│   ├── src/
│   │   ├── components/       # UI, layout, chat
│   │   ├── contexts/         # Auth + Socket
│   │   ├── hooks/            # custom hooks
│   │   ├── pages/            # Next.js pages
│   │   ├── services/         # API client (axios)
│   │   ├── styles/           # global CSS + Tailwind
│   │   ├── types/            # TypeScript types
│   │   └── utils/            # currency, date helpers
│   └── Dockerfile
├── nginx/
│   └── nginx.conf            # reverse proxy config
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## Features

### User System
- Email/password registration with email verification
- Phone number (+962) with OTP verification
- Google OAuth 2.0 sign-in
- Freelancer identity verification (national ID / freelancer permit) — admin approval
- Bilingual profiles (Arabic + English)
- Skills, languages, portfolio sections

### Gig Marketplace (Fiverr-style)
- Create gigs with 3 packages (Basic / Standard / Premium)
- Gallery images, tags, category, city
- Direct order with Stripe Checkout
- Delivery workflow: submit → client review → approve/dispute

### Project Marketplace (Upwork-style)
- Post projects with budget range, duration, skills
- Freelancers submit proposals with cover letter + bid
- Client accepts proposal → contract auto-created
- Milestone-based payments with Stripe

### Payments & Escrow
- All payments via Stripe Checkout (no card data stored)
- Funds held in escrow until client approves delivery
- 10% platform commission auto-computed
- Withdrawal methods: Bank Transfer (IBAN), CliQ (alias), Zain Cash, Orange Money
- Manual admin withdrawal approval

### Real-time Chat
- Socket.IO rooms per contract/order
- Typing indicators, read receipts
- Online presence tracking
- File attachment support

### Admin Dashboard
- Stats: users, gigs, orders, commissions, pending items
- User management: verify identity, suspend/activate
- Withdrawal approval queue
- Transaction log
- Platform settings (commission rate, etc.)

### Bilingual PDF
- Payment proof in A4
- Amount in numbers + English words + Arabic words
- RTL Arabic layout with Noto Sans Arabic font

---

## Stripe Webhook (Local Development)

Use the Stripe CLI to forward webhooks to your local backend:

```bash
stripe listen --forward-to http://localhost:3001/payments/webhook
```

Copy the webhook secret it shows and set it as `STRIPE_WEBHOOK_SECRET` in `.env`.

---

## Stopping / Resetting

```bash
# Stop all services
docker-compose down

# Stop and remove all data volumes (full reset)
docker-compose down -v
```
