# 07 — Deployment & Go-Live

## Moving off the old host
The previous WordPress site lived on a paid managed host. Since the new site is fully static on the frontend, it moved to **Cloudflare Pages**, which removed the hosting cost entirely.

## DNS
- The domain stays registered at Porkbun.
- Nameservers were switched to Cloudflare so the domain can be attached to Pages.
- HTTPS certificates are issued and renewed automatically.

## Backend
- Supabase project hosted in the EU (Frankfurt) — close to guests and the property, and a sensible default for GDPR.
- Database migrations, RLS policies and the `pg_cron` schedule are applied through the Supabase SQL editor / migrations.
- Edge functions deployed: `quote`, `create-checkout`, `stripe-webhook`, `ical-import`, `ical-export`.

## Launch checklist
- [x] All placeholder copy replaced in `content.js`, CIN code displayed on listings
- [x] Photos resized and compressed for fast loading
- [x] Admin account created, public sign-ups disabled
- [x] Stripe end-to-end tested in test mode
- [x] Stripe switched to live keys + live webhook endpoint
- [x] Customer receipts enabled in Stripe
- [x] Airbnb export URL added on Airbnb for both apartments
- [x] Airbnb import URLs added in the dashboard, first sync verified
- [x] Test booking on the website confirmed it appears as blocked on Airbnb, and vice versa

## Running costs
| Service | Cost |
|---|---|
| Cloudflare Pages + DNS | Free |
| Supabase | Free tier |
| Stripe | Per-transaction fees only (no monthly fee) |
| Domain | Annual renewal |

The 15-minute sync job also keeps the free-tier Supabase project active, so it never pauses for inactivity.
