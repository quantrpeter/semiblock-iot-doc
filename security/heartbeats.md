# Status, Heartbeats, and Offline Detection

The platform does not maintain persistent connections from devices. Instead it uses a simple heartbeat model based on the last successful authenticated call.

## What counts as a heartbeat

- Any successful `POST /iot/data` (the normal telemetry path).
- Any successful `POST /iot/commands/poll` (the lightweight "I only want to receive commands" path).

Both paths call the same `authenticateDevice` helper and then do:

```php
$device->update(['status' => 'online', 'last_seen_at' => now()]);
```

## How "online" / "offline" is derived

Today `status` is a simple column that is set to `online` on the first successful contact and never automatically reverts to `offline`. The UI may apply a soft threshold (e.g. "if last_seen_at is more than 24 h old, treat as offline for display") or may simply show the literal status plus the relative last-seen time.

A future background job could periodically scan for devices whose `last_seen_at` is older than a configurable threshold and set their status back to `offline`, but the authoritative signal is always the timestamp.

## Using last-seen in workflows

A workflow trigger or condition can compare the current time against a device's stored `last_seen_at`:

- "Greenhouse sensor has not reported in the last 30 minutes" → send an alert, queue a command to the watchdog board, etc.
- This is usually implemented by a scheduled (cron-style) workflow that scans devices rather than a per-reading trigger.

## What does *not* update last_seen

- Failed auth attempts (401 is returned before the row is touched).
- User activity in the web UI.
- Workflow side-effects (Sheets writes, external HTTP calls).
- Manual "Run workflow" clicks.

Only the device itself, presenting valid credentials, can prove it is still alive.

## Classroom implications

- A board that is intentionally powered down for the weekend will naturally age into "offline".
- A board that is on but whose firmware has crashed or whose Wi-Fi has dropped will stop heartbeating; the teacher sees the stale last-seen and can investigate.
- Because there is no "I am shutting down cleanly" message, you cannot distinguish "device deliberately went to sleep for an hour" from "device lost power" using only the heartbeat. If you need that distinction, have the device send a final "goodbye" reading (with a sensor_type like `lifecycle`) before it sleeps, and treat the absence of that reading as a hint.

## Tuning expectations

For a weather station that reports every 5 minutes, a last-seen of "12 minutes ago" is normal. For a robot that is expected to be in constant radio contact during a demo, the same value means something is wrong. The platform gives you the raw data; the interpretation is up to your workflows, dashboards, and operating procedures.