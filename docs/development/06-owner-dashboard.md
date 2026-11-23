# 06 — Owner Dashboard

`admin.html` is a single page that only works after logging in with Supabase Auth, and only for accounts listed in the `admins` table. Public sign-up is disabled.

## Bookings
- Upcoming / past / cancelled tabs.
- Guest name, email, phone, dates, guests, total and source.
- Link straight to the payment in the Stripe dashboard.
- Revenue and occupancy stats.
- **+ Manual booking** for reservations taken by phone or email; these are protected by the same overlap constraint as online bookings.
- Cancelling a booking reopens the dates. Refunds are issued from Stripe deliberately, so money is never moved by accident.

## Calendar
- 12-month view per apartment, color-coded: website booking, awaiting payment, Airbnb, owner block.
- Click-and-drag to block dates for personal use.

## Prices & settings
- Nightly price, cleaning fee, city tax rate and cap, min/max nights, max guests, check-in/out times.
- **Seasonal prices** with date ranges that override the base rate.
- Changes apply to new quotes immediately because pricing is always computed server-side.

## Airbnb sync
- Per-apartment import URL field and the export URL to copy into Airbnb, with step-by-step instructions on the card itself.
- Last sync time, status and a **Sync now** button.

## Design goals
The owner is not technical and often abroad, so the dashboard favours plain language, large touch targets for mobile, and confirmation prompts for anything destructive.
