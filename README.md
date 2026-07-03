# Heirs

**Heirs is a UAE estate-planning platform for expats.** It helps people create a
legally sound, court-registered will for their assets in the UAE, entirely online,
in about the time it takes to fill in a form.

The experience is calm, editorial, and trust-first by design. Estate planning is a
serious, emotional decision, so the interface stays quiet and out of the way. See
[`DESIGN.md`](DESIGN.md) for the full design language.

## The problem it solves

If you live in the UAE without a registered UAE will, your estate can be distributed
under UAE personal status law, which often differs sharply from your home country's
rules. For the millions of expats in the country, that means a spouse, children,
property, and bank accounts can end up frozen or divided in ways the person never
intended. The traditional fix (law firms and in-person notarisation) is slow and
expensive.

Heirs turns that into a guided online flow: answer a questionnaire, get documents
drafted, and register them through the ADJD or DIFC courts, at a fraction of the
usual cost with no marked-up court fees.

## Who it's for

Expat individuals and couples living in the UAE who have assets, property, or
children here and want peace of mind that their wishes are legally protected.

## How it works

1. **Guided intake** (`/start`) — a 10 to 15 minute questionnaire captures the estate,
   beneficiaries, guardianship wishes, and executor details.
2. **Document generation** (`/will`) — Heirs prepares the will document from the
   intake, ready for review.
3. **Checkout** (`/checkout`) — the user pays the Heirs fee in AED via Ziina. Court
   fees are paid separately to the courts and are never marked up.
4. **Court registration** — the will is registered through the ADJD (Abu Dhabi) or
   DIFC courts, typically within one to three weeks.
5. **Dashboard + vault** — the signed documents live in an encrypted document vault,
   with an annual reminder to review and keep the will current.

## What's included

| Plan | Price | What you get |
| --- | --- | --- |
| **UAE Will** | AED 799 + AED 950 court fee | Full UAE will drafted and registered, guardianship for children, vault storage, annual review reminder |
| **Mirror Wills** | AED 1,199 + AED 1,900 court fee | Two matched wills for couples, both registered, plus everything above |
| **Complete Package** | From AED 1,699 | UAE will + home-country will (UK or India), covers assets abroad, power of attorney included, priority support |

Court fees are payable directly to the UAE courts and are never marked up.

## Product surface

- **Marketing + content** (`/`, `/blog`, `/help`) — landing page, SEO content, and an
  AI-assisted article generator (Together AI) run from the internal ops console.
- **Will flow** (`/start` → `/will` → `/checkout`) — the core guided experience.
- **Authenticated dashboard** (`/dashboard`) — will status, documents, and the vault.
- **Internal ops** (`/ops`) — article management and back-office tooling.
- **Auth** — email/password, Google OAuth, and UAE Pass OIDC.

## Tech stack

- **[Next.js 16](https://nextjs.org/)** (App Router) + **React 18** + **TypeScript**
- **[Tailwind CSS](https://tailwindcss.com/)** with `@tailwindcss/typography`
- **[Prisma](https://www.prisma.io/)** ORM on **PostgreSQL**
- **[Auth.js / NextAuth v5](https://authjs.dev/)** — email/password, Google OAuth, UAE Pass OIDC
- **[Ziina](https://ziina.com/)** for payments (AED)
- **[Together AI](https://www.together.ai/)** for the article generator
- Encrypted S3-compatible storage for the document vault

## Getting started

### Prerequisites

- Node.js 20+ (the project is developed on Node 24)
- A PostgreSQL database, locally via Docker:

  ```bash
  docker compose up -d
  ```

### Setup

```bash
# 1. Install dependencies (runs `prisma generate` automatically)
npm install

# 2. Configure environment
cp .env.example .env.local
# then fill in the values, see "Environment" below

# 3. Apply the database schema
npx prisma migrate dev

# 4. Start the dev server
npm run dev
```

## Scripts

| Script | Description |
| --- | --- |
| `npm run dev` | Start the Next.js dev server |
| `npm run build` | Production build |
| `npm run start` | Run the production build |
| `npm run lint` | Lint with ESLint |

## Environment

Copy `.env.example` to `.env.local` and fill in the values. Key groups:

- **App** — `NEXT_PUBLIC_APP_URL`
- **Database** — `DATABASE_URL` (PostgreSQL)
- **Auth** — `AUTH_SECRET`, Google OAuth, UAE Pass OIDC
- **Payments** — `ZIINA_API_KEY` (leave blank for local demo mode)
- **Email** — `RESEND_API_KEY`, `EMAIL_FROM`
- **Together AI** — `TOGETHER_API_KEY`, `TOGETHER_MODEL`
- **Document vault** — encrypted S3-compatible storage in a UAE region

See [`.env.example`](.env.example) for the full annotated list.

## Project structure

```
src/
  app/            # App Router pages and API routes
    start/        # Guided will-intake flow
    will/         # Will details + generated document
    checkout/     # Ziina checkout + completion
    dashboard/    # Authenticated user dashboard
    ops/          # Internal ops (articles, etc.)
    blog/ help/   # Marketing + content pages
    api/          # Route handlers (auth, checkout, webhooks, will)
  components/     # UI components
  lib/            # Prisma, auth, payments, email, AI, recommendation
  constants/      # Static content/data
  types/          # Shared TypeScript types
prisma/           # Prisma schema
```

## Deployment

Deployment config lives in [`.do/app.yaml`](.do/app.yaml) (DigitalOcean App Platform).
Set the production environment variables there, and use a UAE-region managed Postgres
at launch.
