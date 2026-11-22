# 03 — Database Design

## Core tables
- **properties** — one row per apartment: slug, name, nightly price, cleaning fee, city tax rate and cap, min/max nights, max guests, check-in/out times.
- **seasonal_prices** — date ranges that override the base nightly price (summer, holidays).
- **bookings** — guest details, property, check-in/check-out, guest count, total price, status (`pending`, `confirmed`, `cancelled`), source (`website`, `manual`), Stripe session ID, hold expiry.
- **blocked_dates** — owner blocks and imported Airbnb reservations, tagged with their source.
- **ical_feeds** — the Airbnb import URL per property, last sync time and status.
- **admins** — maps Supabase Auth users to dashboard access.

## Preventing double bookings
The most important requirement was that two overlapping stays can never exist for the same apartment — including when two guests click "pay" at the same moment.

Checking availability in code and then inserting is a race condition. Instead, the guarantee is enforced by Postgres itself with an **exclusion constraint** on a date range:

```sql
create extension if not exists btree_gist;

alter table bookings add constraint no_overlap
  exclude using gist (
    property_id with =,
    daterange(check_in, check_out, '[)') with &&
  ) where (status in ('pending', 'confirmed'));
```

- The `[)` half-open range means check-out day is excluded, so **back-to-back stays** (one guest leaves, another arrives the same day) are allowed.
- Cancelled bookings are excluded from the constraint, so cancelling immediately frees the dates.
- Pending bookings are included, so a guest who is mid-payment holds the dates.

## Security: Row Level Security
- Public (anonymous) users can read property info, prices and a **sanitized availability view** — dates only, no guest data.
- Public users **cannot** insert bookings directly; all writes go through edge functions using the service role.
- Only users present in `admins` can read bookings and change settings.

## Lessons
Pushing the invariant down into the database simplified everything above it — the booking function can just attempt the insert and translate a constraint violation into a friendly "those dates were just taken" message.
