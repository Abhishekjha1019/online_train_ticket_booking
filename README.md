# Saved Booking Profile

A local, encrypted profile for preparing IRCTC Tatkal bookings. It stores passengers,
journeys, berth and quota choices, and payment preparation, so you don't re-enter them
under time pressure.

## What it does

- **Passengers:** up to 4 saved profiles (name, age, gender, berth). Passenger 1 defaults to Upper Berth, passenger 2 to Lower Berth.
- **Journeys:** Outbound 03 Feb 2027, Train 15566, New Delhi to Supaul; Return 07 Feb 2027, Train 15565, Supaul to New Delhi. Station codes, class, quota order and passenger assignment are saved.
- **Prepare My Booking:** one tap shows the readiness indicator, reservation and Tatkal dates, countdown, route, class, quota, passengers, fare check and wallet reminder, and opens the official IRCTC site.
- **Payment:** IRCTC eWallet is the preferred method. You enter the wallet balance by hand; the app compares it with the estimate.
- **Reminders:** in-page alerts before each Tatkal window (5 to 60 minutes ahead, your choice).
- **Booking attempts:** record attempts. A booking is shown as CONFIRMED only when you enter the 10-digit PNR from IRCTC.
- **Backup and restore:** encrypted JSON file. Passwords, OTPs and the wallet balance are never included.

## Security model

- Data is encrypted in the browser with AES-256-GCM. The key is derived from your passphrase with PBKDF2-SHA-256 (310,000 iterations). The passphrase is never stored.
- Only ciphertext is kept in `localStorage`. Forgetting the passphrase means the data cannot be recovered; keep a backup.
- The page has a Content-Security-Policy with `connect-src 'none'`: no network requests of any kind. Nothing is sent to any server or AI service.
- The app never asks for or stores the eWallet transaction password or OTP, and never performs a payment or login on IRCTC. Those stay with you on the official site.
- A ticket is never marked booked by the app. Status stays NOT CONFIRMED until you record an IRCTC PNR.

## Run it

Requires a modern browser. No install step is needed for the app itself.

```
cd ONLINE_BOOKING_SYSTEM
python -m http.server 8080
```

Open http://localhost:8080 . Web Crypto works on `localhost` and HTTPS. It does not work on a plain `http://` LAN address, so a phone needs the app served over HTTPS.

Run the tests (Node 20 or newer):

```
npm test
```

## Facts to check before relying on them

- **Reservation (ARP) date** is calculated as journey date minus 60 days. The counting rule can differ by a day, so verify it on IRCTC.
- **Tatkal opening** is calculated as one day before the journey: 10:00 IST for AC classes, 11:00 IST for sleeper.
- **Station code for Supaul** and **classes** were not provided and are left blank. Enter them from IRCTC.
- **Fares** are not fetched. Enter the per-passenger fare shown on IRCTC; the total is an estimate.

## Files

| Path | Purpose |
| --- | --- |
| `index.html` | Page shell and CSP |
| `css/styles.css` | Mobile-first styles, light and dark |
| `js/domain.js` | Dates, validation, readiness, fare check, backup redaction (no DOM) |
| `js/vault.js` | Passphrase key derivation, AES-GCM encryption, backup format |
| `js/app.js` | UI and actions |
| `tests/` | Unit tests for dates, readiness, encryption and redaction |
