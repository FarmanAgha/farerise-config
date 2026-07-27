# fareboost-config

Runtime parser settings for the FareBoost Android app.

This repository is public because the app fetches `config.json` over plain HTTPS with no
authentication. It contains **no personal data and no credentials** — only the keywords and
thresholds the offer parser uses to recognise a ride offer on screen.

## Why it exists

FareBoost reads offer cards from Uber, Lyft and Hopp on-device. Those apps change their
layouts and rename their ride tiers without notice, and each time that happens a hard-coded
keyword or threshold in the app can stop matching. Previously every such fix needed a new app
release, and every install blinds offer detection for 30-70 seconds. Editing this file fixes
it for every install instead.

## How the app treats it

- Every key is **optional**. Anything absent, empty, or the wrong type falls back to the value
  compiled into the app.
- A malformed file, a 404, or no network **never** changes behaviour — the app keeps its
  built-in values and logs that it did.
- The file is fetched on app start and at most every 6 hours, on a background thread. It is
  never read on the offer-detection path.

Bump `version` on every edit; the app logs it, so the running config can be identified from a
device log.

## Keys

| Key | Type | Purpose |
|---|---|---|
| `uber_tier_keywords` | string[] | Substrings that prove an Uber screen is an offer card. The most churn-prone constant in the app. |
| `rating_fare_guard_enabled` | bool | Kill switch for the guard that rejects a fare exactly equal to the rider's rating. |
| `rating_fare_guard_min` | number | Lower bound of the band that guard applies to. |
| `rating_fare_guard_max` | number | Upper bound of the band that guard applies to. |
