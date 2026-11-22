# 05 — Two-Way Airbnb Calendar Sync

Airbnb doesn't offer a public booking API for small hosts, but it does support the **iCalendar (.ics)** standard for importing and exporting availability. The sync is built on that.

## Airbnb → website (import)
- Each apartment stores its Airbnb calendar export URL.
- An `ical-import` edge function fetches the feed, parses each `VEVENT`, and upserts the dates into `blocked_dates` with source `airbnb`.
- Reservations that disappear from the feed (cancelled on Airbnb) are removed, so the website calendar always mirrors Airbnb.
- `pg_cron` runs the import **every 15 minutes**. The dashboard also has a **Sync now** button and shows last sync time and status.

## Website → Airbnb (export)
- An `ical-export` edge function serves a public `.ics` feed per apartment containing confirmed website bookings, manual bookings and owner blocks.
- That URL is added in Airbnb under *Import calendar*, so Airbnb blocks those dates.
- The feed contains only dates — no guest names or contact details.

## The sync window problem
Airbnb only refreshes imported calendars every few hours. That leaves a small window where the same dates could be booked on both channels. Options considered:
- Polling faster on my side (done — 15 minutes), but this doesn't help the Airbnb side.
- A channel manager service (paid, overkill for two apartments).

The accepted trade-off: the window is small and rare in practice, and the dashboard makes conflicts visible immediately so one side can be cancelled and refunded.

## Parsing details
- All-day events use `DTSTART;VALUE=DATE` / `DTEND;VALUE=DATE`, where `DTEND` is exclusive — which matches the half-open date ranges used in the database.
- Feeds are fetched with a timeout, and a failed fetch keeps the previous data rather than wiping the calendar.
