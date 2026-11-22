# 02 — Architecture & Tech Stack

## Decision: static frontend + managed backend
Rather than keep WordPress and bolt on a booking plugin, I chose to build a lightweight custom site. Booking plugins were either paid, heavy, or didn't handle two-way iCal sync and payment holds the way I wanted.

```
Browser ──► Static site (HTML/CSS/JS) on Cloudflare Pages
              │
              ├──► Supabase (Postgres + Auth + Row Level Security)
              │
              └──► Supabase Edge Functions (Deno/TypeScript)
                        ├──► Stripe Checkout + webhooks
                        └──► Airbnb iCal (import / export)
```

## Stack
| Layer | Choice | Why |
|---|---|---|
| Frontend | Vanilla HTML, CSS, JavaScript | No build step, fast, trivially hostable |
| Hosting | Cloudflare Pages | Free, global CDN, automatic HTTPS |
| DNS | Cloudflare (domain registered at Porkbun) | Needed for Pages; free |
| Database | Supabase Postgres | Real relational DB, constraints, free tier |
| Auth | Supabase Auth | Owner-only login for the dashboard |
| Server logic | Supabase Edge Functions | Keeps secrets and pricing off the client |
| Payments | Stripe Checkout | Hosted, PCI-compliant, no monthly fee |
| Scheduling | `pg_cron` | Runs the Airbnb sync every 15 minutes |

## Principles
- **The client is never trusted.** Prices, availability and booking status are all decided on the server.
- **The database is the last line of defence.** Overlap protection lives in a Postgres constraint, not just in application code.
- **Content is separated from code.** All text and photo lists live in a single `content.js` file so copy can be edited without touching layout or logic.

## Project layout
```
site/
  index.html        home page
  stay.html         apartment page (?p=1bedroom / ?p=2bedroom), calendar + booking
  success.html      post-payment confirmation
  admin.html        owner dashboard
  js/content.js     all text + photo lists
  js/config.js      Supabase public connection settings
  img/              optimized photos
supabase/functions/ edge functions
```
