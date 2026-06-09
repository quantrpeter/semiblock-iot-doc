# Changelog

All notable changes to the SemiBlock IoT Platform will be documented here.

## Unreleased / current development

- Initial public documentation set in the `semiblock-iot-doc` repository.
- Core features implemented in the Laravel + React monolith:
  - Device registration with auto-generated `device_id` + `secret_key`.
  - Schema-less ingest at `POST /iot/data` (body or header auth).
  - Command polling and delivery in the ingest response.
  - Personal dashboard with draggable, resizable Chart.js widgets (line, timeseries, bar, doughnut).
  - Automatic numeric field discovery for the widget form.
  - Google Sheets per-device auto-append via service account.
  - Visual node-graph workflows with reading triggers and side-effect actions.
  - Full Data Explorer (search + filter + server pagination) across all of a user's readings.
  - `last_seen_at` + status tracking updated on every authenticated device call.

## 2026 (initial platform launch)

- First classroom deployments.
- MicroPython editor blocks (`iotConnect`, `iotPushReading`, `iotPushValue`) added.
- Simulator support for the IoT blocks so students can develop logic without hardware.

---

*Older entries will be back-filled as the platform matures. For the absolute latest, see the git history of `newblock-server` and the `iotPlatform/` SPA.*