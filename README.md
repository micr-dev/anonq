# AnonQ

> Anonymous Q&A platform — visitors ask questions anonymously, admins answer publicly.

[![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue.svg)](https://www.typescriptlang.org/)
[![Next.js](https://img.shields.io/badge/Next.js-15-black.svg)](https://nextjs.org/)
[![License: ISC](https://img.shields.io/badge/License-ISC-green.svg)](LICENSE)

## Overview

AnonQ is a privacy-focused Q&A platform built with Next.js. Visitors can submit anonymous questions without creating an account, while administrators manage and answer questions through a secure dashboard.

**Tagline:** *No strings, no names. Just curiosity.*

### Key Features

- **100% Anonymous Submissions** — No login required to ask questions
- **AI-Powered Text Refinement** — Optional grammar and style correction using OpenAI (or custom API)
- **Admin Dashboard** — Auth0-protected interface to answer and manage questions
- **Real-time Feed** — Q&A feed updates every 30 seconds with no page refresh
- **Push Notifications** — Optional ntfy.sh integration for new question alerts
- **Dark Theme UI** — Sleek terminal-inspired aesthetic with glitch effects

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | [Next.js 15](https://nextjs.org/) |
| Language | [TypeScript](https://www.typescriptlang.org/) |
| UI | [Tailwind CSS 4](https://tailwindcss.com/) + [Base UI](https://base-ui.com/) |
| Database | [Supabase](https://supabase.com/) (PostgreSQL) |
| Auth | [Auth0](https://auth0.com/) |
| AI | OpenAI API or Custom API |
| Notifications | [ntfy.sh](https://ntfy.sh/) |

## Project Structure

```
anonq/
├── app/
│   ├── admin/
│   │   ├── page.tsx           # Admin login page
│   │   └── dashboard/
│   │       └── page.tsx       # Admin dashboard
│   ├── api/
│   │   ├── questions/         # Public API routes
│   │   │   ├── route.ts      # POST /api/questions
│   │   │   ├── qa/route.ts   # GET /api/questions/qa
│   │   │   └── regenerate/route.ts
│   │   └── admin/             # Protected API routes
│   │       ├── questions/route.ts
│   │       └── answer/route.ts
│   ├── page.tsx               # Public home page
│   └── layout.tsx             # Root layout
├── components/
│   ├── ui/                    # Reusable UI components (40+)
│   ├── HomeClient.tsx         # Public-facing UI
│   ├── AdminDashboardClient.tsx
│   ├── QuestionForm.tsx       # Submission form with AI refinement
│   ├── QAList.tsx             # Q&A feed display
│   └── FaultyTerminal.tsx     # Animated background effect
├── lib/
│   ├── api.ts                 # API client
│   ├── auth0.ts               # Auth0 configuration
│   ├── supabase.ts            # Supabase client
│   ├── types.ts               # TypeScript interfaces
│   ├── config.ts              # Environment config
│   └── data/
│       └── questionService.ts # Database operations
├── hooks/
│   └── use-mobile.ts
├── middleware.ts              # Auth0 middleware
└── public/                     # Static assets
```

## Getting Started

### Prerequisites

- Node.js 18+
- npm or pnpm
- Supabase account
- Auth0 account

### Installation

```bash
# Clone the repository
git clone https://github.com/micr-dev/anonq.git
cd anonq

# Install dependencies
npm install

# Copy environment template
cp .env.example .env

# Configure environment variables (see below)
```

### Environment Variables

Create a `.env` file with the following variables:

| Variable | Required | Description |
|----------|----------|-------------|
| `AUTH0_SECRET` | Yes | Generate with `openssl rand -hex 32` |
| `AUTH0_DOMAIN` | Yes | Your Auth0 tenant domain |
| `AUTH0_CLIENT_ID` | Yes | Auth0 application client ID |
| `AUTH0_CLIENT_SECRET` | Yes | Auth0 application client secret |
| `APP_BASE_URL` | Yes | Production URL (e.g., `https://anonq.micr.dev`) |
| `NEXT_PUBLIC_SITE_URL` | No | Public canonical URL (defaults to `APP_BASE_URL`) |
| `NEXT_PUBLIC_SUPABASE_URL` | Yes | Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Yes | Supabase anon/public key |
| `ALLOWED_ADMIN_EMAILS` | No | Comma-separated admin emails |
| `API_PROVIDER` | No | `openai` or `custom` (default: `openai`) |
| `OPENAI_API_KEY` | Yes* | OpenAI API key (*if using OpenAI) |
| `CUSTOM_API_URL` | Yes* | Custom API endpoint (*if using custom) |
| `CUSTOM_API_KEY` | Yes* | Custom API key (*if using custom) |
| `NTFY_URL` | No | ntfy.sh topic URL for push notifications |

### Running Locally

```bash
# Development
npm run dev

# Production build
npm run build
npm start
```

The app runs at `http://localhost:3000` by default.

## Database Schema

AnonQ uses Supabase (PostgreSQL) with two main tables:

### `questions`

| Column | Type | Description |
|--------|------|-------------|
| `id` | UUID | Primary key |
| `content` | TEXT | Question content |
| `answered` | BOOLEAN | Whether the question has been answered |
| `timestamp` | TIMESTAMPTZ | Submission time |

### `answers`

| Column | Type | Description |
|--------|------|-------------|
| `id` | UUID | Primary key |
| `question_id` | UUID | Foreign key to questions |
| `content` | TEXT | Answer content |
| `timestamp` | TIMESTAMPTZ | Answer time |

Row Level Security (RLS) policies restrict write access to authenticated users.

## Auth0 Configuration

### Application Settings

Create an Auth0 Regular Web Application and configure:

| Setting | Value |
|---------|-------|
| Allowed Callback URLs | `https://anonq.micr.dev/api/auth/callback` |
| Allowed Logout URLs | `https://anonq.micr.dev` |
| Allowed Web Origins | `https://anonq.micr.dev` |
| Application Login URI | `https://anonq.micr.dev` |

For local development, also add:
- `http://localhost:3000/api/auth/callback`
- `http://localhost:3000`
- `http://localhost:3000` (Web Origins)

### Admin Access

Add admin emails to `ALLOWED_ADMIN_EMAILS` (comma-separated). Users with these emails can access `/admin/dashboard`.

## API Reference

### Public Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/questions` | Submit a new question |
| `GET` | `/api/questions/qa` | Get all answered Q&A pairs |
| `POST` | `/api/questions/regenerate` | Refine text using AI |

### Admin Endpoints (Requires Auth)

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/admin/questions` | Get all questions |
| `POST` | `/api/admin/answer` | Post an answer |
| `DELETE` | `/api/admin/questions` | Delete a question |

## Deployment

### Vercel (Recommended)

1. Push to GitHub
2. Import project in [Vercel](https://vercel.com)
3. Configure environment variables
4. Deploy

### Docker

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

## Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start development server |
| `npm run build` | Create production build |
| `npm run start` | Start production server |

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the ISC License. See [LICENSE](LICENSE) for details.

## Acknowledgments

- [Base UI](https://base-ui.com/) for component primitives
- [Tailwind CSS](https://tailwindcss.com/) for styling
- [Supabase](https://supabase.com/) for backend infrastructure
