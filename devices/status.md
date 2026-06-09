# Device Status and Last-Seen Tracking

Every device row carries two pieces of liveness information that are updated automatically by the platform.

## `status` column

- `offline` — never successfully authenticated, or explicitly set by an admin action (rare).
- `online` — the device has successfully called either `/iot/data` or `/iot/commands/poll` at least once.

The status is a simple derived flag; it is **not** a persistent "connected" socket state. A device that pushes once per hour will still show `online` even while it is sleeping.

## `last_seen_at`

Updated on every successful authenticated request (ingest or poll). This is the authoritative "when did we last hear from this device" timestamp.

The UI shows it in relative form ("2 minutes ago", "3 days ago") and also as an absolute value on hover or in the detail view.

## How the dashboard and Data Explorer use it

- The Devices list is usually sorted by `last_seen_at desc` so the most recently active devices are at the top.
- Some workflow trigger nodes can test "device has not reported in N minutes" by comparing against the stored `last_seen_at`.
- The "offline" visual treatment on a device card is based on `last_seen_at` being older than a soft threshold (currently just the status flag, but the timestamp is what matters for logic).

## What does *not* update last_seen

- Failed authentication attempts (they return 401 before touching the row).
- User browsing the dashboard or Data Explorer.
- Workflow execution that does not involve a device-originated request.
- Google Sheets appends (those are server-side side-effects after a successful ingest).

## Manual "mark offline"

There is currently no UI button to force a device back to offline. If you need to simulate a long absence for testing workflows, the pragmatic way is to temporarily change the system clock or simply wait. A future admin tool may expose a direct update.