# 04 — Booking Flow & Stripe Payments

## Flow
1. Guest selects dates and guest count on the apartment page.
2. The frontend calls a `quote` edge function. The server calculates the price: nightly or seasonal rate × nights, plus cleaning fee, plus Como city tax (€ per guest per night, capped at a maximum number of nights).
3. **Reserve & pay** calls `create-checkout`, which:
   - re-validates dates, minimum/maximum nights and guest count,
   - recalculates the price (the client's number is never used),
   - inserts a `pending` booking with a ~30 minute hold,
   - creates a Stripe Checkout Session and returns its URL.
4. The guest pays on Stripe's hosted checkout page.
5. Stripe calls the `stripe-webhook` function:
   - `checkout.session.completed` → booking becomes `confirmed`.
   - `checkout.session.expired` → booking is cancelled and the dates are released.
6. The guest lands on `success.html` with their booking summary.

## Why hosted Checkout
Stripe Checkout handles card entry, 3-D Secure / SCA (required for European cards), Apple Pay / Google Pay and receipts. Card data never touches my code, which keeps PCI scope minimal.

## Webhook security
The webhook verifies Stripe's signature using the signing secret before trusting any event. Handling is **idempotent** — receiving the same event twice has no extra effect — because Stripe retries deliveries.

## Abandoned payments
If a guest closes the tab, the hold expires on its own. If they click "back" from Checkout, the hold is released immediately so the dates reopen without waiting.

## Secrets
`STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET` and `SITE_URL` are stored as Supabase function secrets and are never shipped to the browser.

## Testing
Built and verified end to end in Stripe test mode (test card `4242 4242 4242 4242`), including expired sessions and duplicate webhook deliveries, before switching to live keys and a live webhook endpoint.
