# 01 — Project Scope

## Background
Blue View Como is a pair of short-term rental apartments on Lake Como, Italy (a one-bedroom and a two-bedroom). Until now, guests could only book through Airbnb, and the domain `blueviewcomo.com` pointed at a basic WordPress brochure site with no booking capability.

The goal of this project is to replace that site with a custom direct-booking platform so guests can book and pay on the website itself, avoiding platform commission while keeping Airbnb as a second sales channel.

## Requirements
- **Listing pages** for each apartment: photo gallery, description, amenities, house rules, cancellation policy.
- **Live availability calendar** that reflects bookings from both the website and Airbnb.
- **Online payment** at the time of booking.
- **No double bookings** — ever — even with two channels and concurrent guests.
- **Two-way Airbnb calendar sync**, so a booking on one channel blocks the dates on the other.
- **Owner dashboard** for viewing bookings, blocking dates, adjusting prices and seasonal rates.
- **Low running cost** — ideally free hosting, with only per-transaction payment fees.
- **Legal compliance** — Italian short-term rentals must display their CIN (national identification code), and Como charges a per-guest, per-night city tax that has to be included in the price.

## Non-goals
- Multi-property marketplace features.
- Guest accounts / logins (guests book as guests; only the owner logs in).
- Replacing Airbnb entirely.

## Constraints
- The owner lives abroad part of the year, so the dashboard has to be usable on its own without technical knowledge.
- The Airbnb listings belong to the owner, so any Airbnb-side setup has to be simple enough to hand over as written steps.
