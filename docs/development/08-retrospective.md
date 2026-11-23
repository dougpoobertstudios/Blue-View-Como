# 08 — Retrospective

## What went well
- **Database-enforced integrity.** Using a Postgres exclusion constraint for overlapping stays removed an entire class of race-condition bugs and made the booking code much simpler.
- **Server-side pricing.** Keeping all price logic in one edge function meant seasonal rates, cleaning fees and city tax only had to be correct in one place.
- **Hosted payments.** Stripe Checkout handled SCA, wallets and receipts without any custom payment UI.
- **Near-zero running cost.** Moving to Cloudflare Pages + Supabase free tier replaced a monthly hosting bill.

## Challenges
- **iCal is a lowest-common-denominator protocol.** No push notifications, no booking details, and Airbnb's slow refresh creates an unavoidable sync window. Designing around it (fast polling on my side, clear conflict visibility in the dashboard) was the practical answer.
- **Payment holds.** Getting the pending → confirmed / expired lifecycle right — including guests who abandon checkout, and Stripe retrying webhooks — took careful idempotent handling.
- **Off-by-one dates.** Check-out days, exclusive `DTEND` in iCal and timezone handling all had to agree. Standardising on half-open date ranges everywhere fixed it.
- **Building for a non-technical owner.** Every admin feature had to be self-explanatory without me there.

## What I'd do next
- Branded confirmation emails to guests and new-booking alerts to the owner (e.g. via Resend).
- Multi-language content (Italian / English) for the guest-facing pages.
- Automated tests for the pricing and booking functions.
- Optional deposit / partial payment for long stays.

## Skills used
Postgres schema design, Row Level Security, serverless TypeScript (Deno), Stripe Checkout & webhooks, iCalendar parsing, scheduled jobs, DNS/hosting configuration, and vanilla JavaScript UI development.
