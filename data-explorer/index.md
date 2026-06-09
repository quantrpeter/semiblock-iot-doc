# Data Explorer (All Readings)

The Data page (reachable from the IoT sidebar) shows a paginated, filterable, searchable table of *every* reading that any of your devices have ever sent.

It is the "raw SQL view" for power users and teachers who want to audit or export data without going through Google Sheets.

## Features

- Full-text search across device name, sensor_type, and the JSON payload.
- Filter by specific device or sensor_type (the facets are populated from your actual data).
- Sort by recorded time, sensor type, or device name.
- Server-side pagination (default 20 rows, up to 100).
- The same data that powers the dashboard widgets is visible here in tabular form.

## Columns shown

- Timestamp (`recorded_at`)
- Device (name + public id)
- Sensor type
- The entire `payload` rendered as pretty JSON (or a collapsed view)

## When to use it vs. the Dashboard

- Use **Dashboard** for at-a-glance live charts during a demo or lab.
- Use **Data Explorer** when you need to:
  - Find a specific anomalous reading
  - Verify that a device really did send data at a certain time
  - Hand a student the exact JSON their board produced
  - Debug a workflow that is supposed to react to certain payload shapes

For long-term archival or sharing with people who do not have SemiBlock accounts, connect a Google Sheet instead (see [Integrations](../integrations/google-sheets.md)).

## API backing this page

`GET /iot/device-data?...` (see the [query API](../data/query-api.md) once written). The response also returns the current list of your devices and distinct sensor types so the UI can render the filter dropdowns without an extra round-trip.