# Owner-Side Data Query APIs

These endpoints are used by the logged-in web UI (Data Explorer, dashboard widgets, latest-value displays, etc.). They all require a normal user session and only return data belonging to devices owned by that user.

## List readings for one device

```
GET /iot/device/{device}/data
```

Query parameters:

- `sensor_type` — filter
- `since` — ISO date, only readings on or after this time
- `limit` — 1–1000 (default 100)

Returns the most recent N rows (by `recorded_at desc`) matching the filters.

## Latest reading per sensor type for one device

```
GET /iot/device/{device}/latest
```

Returns one row per distinct `sensor_type` (the most recent by `recorded_at`). Extremely useful for "current state" widgets or external status pages.

## All readings across all my devices (the Data Explorer backend)

```
GET /iot/device-data
```

This is the powerhouse endpoint. It supports:

- `search` — full-text across device name, sensor_type, and the JSON payload string
- `device_id` — filter to one device (internal id)
- `sensor_type`
- `sort` + `dir` — one of `recorded_at`, `sensor_type`, `device_name`, `id`
- `page` + `per_page` (1–100)

Response also includes `filters.devices` and `filters.sensor_types` so the UI can render the dropdowns without extra calls.

## Device field discovery (for the Add Chart form)

```
GET /iot/device/{device}/fields
```

Returns the set of numeric keys seen in recent payloads, grouped by `sensor_type`. See the [field discovery](../reference/field-discovery.md) page for the exact shape and algorithm.

## Widget data resolution

Widgets do not have their own public "get my series" endpoint in the current design. The frontend calls the device data or latest endpoints (or a small dedicated resolver) with the widget's stored `iot_device_id`, `sensor_type`, `field`, and `max_points`, then the client transforms the result into the Chart.js `labels` / `values` arrays.

All of these endpoints are intentionally simple and server-side paginated/filtered so that even a classroom with thousands of historical readings per student stays responsive.