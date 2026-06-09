# Live Updates and Data Freshness

## How "live" is the dashboard?

- The dashboard is a normal React SPA page.
- When you first load it, each widget fetches its current series (the N most recent points for its configured device/field).
- There is **no automatic WebSocket or Server-Sent Events push** in the current implementation.
- To see new data you either:
  - Hard-refresh the page, or
  - The UI may poll the widget data endpoints on a modest interval (e.g. every 10–30 s) — check the actual `Dashboard.js` code for the current strategy.

For classroom demos this is usually sufficient: the teacher or a student can click refresh or just wait a few seconds and the chart will update.

## Widget-level freshness

Each widget independently knows how many points it asked for (`max_points`). When new readings arrive they simply appear as the newest entries in the series the next time that widget's data is fetched. Old points age out of the window naturally.

## "Last updated" indicators

The UI shows relative timestamps for devices (`last_seen_at`) and for individual readings in the Data Explorer. Widgets themselves do not currently surface a per-widget "data as of" line, but the underlying series data includes the `recorded_at` values so a future enhancement could add a small "as of …" caption under each chart.

## When you really need true real-time

For applications that need sub-second updates (live oscilloscope traces, robotics tele-op, etc.) the current platform is not the right tool. In those cases you would typically:

- Run a local MQTT broker or WebSocket server on the same LAN as the device and the viewer, or
- Use a dedicated industrial IoT platform with a proper pub/sub story.

The SemiBlock IoT Platform is optimised for the "I took a reading every 5–60 seconds and I want to see the trend an hour or a day later" educational use case.

## Commands are near real-time

Because commands are delivered in the *response* to the device's own ingest or poll call, the latency is essentially "one device heartbeat". A workflow that reacts to a reading can queue a command that the *same* device (or another device) will receive the next time it talks to the platform. For many classroom automation scenarios (fan on when hot, light on when dark) this is more than fast enough.