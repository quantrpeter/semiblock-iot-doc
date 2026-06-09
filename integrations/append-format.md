# Google Sheets Row Format and Auto-Append Behaviour

## When a row is appended

- Immediately after a successful `POST /iot/data` (inside the same request, but in a try/catch so a Sheets failure never drops telemetry).
- Also when a workflow explicitly executes a "Append to Google Sheet" action node.

## Columns that are always present

| Column          | Source                              | Example                     |
|-----------------|-------------------------------------|-----------------------------|
| `recorded_at`   | `reading.recorded_at` (device time) | `2026-06-09 14:31:02`       |
| `device_id`     | `device.device_id` (public)         | `dev_a1b2c3...`             |
| `device_name`   | `device.name` (friendly)            | `Greenhouse DHT`            |
| `sensor_type`   | `reading.sensor_type` (may be null) | `dht11`                     |

## Dynamic columns from the payload

Every top-level key inside the `data` object that was sent in the ingest request becomes its own column.

- Keys are used verbatim as column headers (after sanitising for Google Sheets rules).
- Values are written as their JSON types (numbers stay numbers, strings stay strings, booleans become TRUE/FALSE, etc.).
- If a later reading introduces a new key that was not seen before, the service appends the new column to the right of the existing ones (Google Sheets supports this).

Example ingest payload:

```json
{
  "sensor_type": "weather",
  "data": {
    "temp": 19.4,
    "humidity": 67,
    "wind": { "speed": 2.1 }
  }
}
```

Might produce a row with these columns (plus the four always-present ones):

`temp | humidity | wind`

(Note: the current implementation typically stringifies or flattens one level of nesting for `wind`; deeper objects may appear as JSON text in a single cell. Adjust your firmware to emit flat payloads if you want clean columns.)

## Multiple devices writing to one sheet

Perfectly normal. Rows are appended in the order the ingests are processed. Use the `device_name` or `device_id` column (or Google Sheets' built-in Filter / QUERY views) to separate concerns.

## Ordering and duplicates

- Rows are appended in roughly the order the platform processed the ingest requests.
- If a device sends the same logical reading twice (e.g. because it retried after a network blip), you will get two nearly-identical rows. The platform does not attempt de-duplication at the Sheets layer (the database has its own `id` and you can de-dupe there if needed).
- `recorded_at` is the best column to sort by if you want "event time" order rather than "arrival time" order.

## Quotas and failures

If Google returns a rate-limit or permission error, the append is skipped for that reading and an error is logged. The reading itself is safe in `iot_device_data`. Subsequent readings will retry the append (the connection is still considered "active" until you explicitly disconnect it).

For very high volume devices, consider using the Data Explorer API or a direct database export instead of (or in addition to) the Sheets bridge.