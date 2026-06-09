# Device Authentication Model

Understanding who can do what with which credential is essential for both security and for explaining the platform to students.

## Two separate authentication domains

### 1. User session (owner APIs)

- Normal SemiBlock login cookie / session.
- All routes under `/iot` except the two device endpoints go through `AuthCheck`.
- The controllers (`IotDeviceController`, `DashboardWidgetController`, `WorkflowController`, …) call helpers such as `resolveOwnedDevice($request, $id)` which verify that the device belongs to `session()->get('user')->id`.
- A logged-in user can see, edit, and delete only their own devices, widgets, workflows, and Sheets connections.
- They can also see all readings that belong to any of their devices (via the join in `allData`).

### 2. Device secret (ingest & poll)

- No user session involved.
- The device presents `device_id` + `secret_key` (in body or headers).
- `authenticateDevice()` does a lookup + constant-time secret comparison.
- If valid, the device is allowed to:
  - Create a new `IotDeviceData` row for itself.
  - Receive any currently pending commands for itself.
  - Update its own `last_seen_at` and `status`.
- It is **not** allowed to:
  - See other devices.
  - See historical readings (even its own — that is an owner-only API).
  - Create widgets, workflows, or change its own friendly name.
  - Touch any Google Sheet mapping.

## Why this split exists

Tiny microcontrollers cannot reasonably implement OAuth, maintain cookies across deep sleep, or handle browser-style redirects. By giving each device its own long-lived bearer token we make participation trivial (`urequests.post` with three fields) while still having a clean ownership model on the web side.

## Blast radius of a leaked secret

If a student accidentally publishes their device's secret:

- An attacker can push fake readings under that device (polluting its charts and any connected Sheet).
- An attacker can receive any commands that a workflow has already queued.
- An attacker **cannot** see the owner's other devices, read historical data via the owner APIs, or create new workflows.

Mitigation is simple and immediate: rotate the secret (or delete + recreate the device). All prior readings remain for audit and historical charts.

## Future enhancements (not yet implemented)

- Short-lived tokens or per-reading nonces.
- Device "scopes" (some devices only allowed to push, never receive commands).
- Per-device rate limits and anomaly detection on the ingest path.
- Audit log of every ingest and every secret reveal.

For now the model is deliberately minimal and matches the threat model of a classroom or club where the main risk is a student accidentally sharing a secret rather than a sophisticated nation-state attacker.