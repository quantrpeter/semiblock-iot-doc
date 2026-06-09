# Google Sheets Integration

One of the most popular features for classrooms is the ability to have every reading automatically appear in a Google Spreadsheet that the teacher or students already know how to use.

## How it works (high level)

1. You create (or reuse) a Google Sheet.
2. In the IoT console → Settings (or the per-device row on the Data page) you paste the spreadsheet ID or full URL.
3. The platform tells you the email address of its Google Service Account.
4. You share the sheet with that address and give it **Editor** permission.
5. From that moment on, every time a device successfully pushes a reading, a background job appends a row containing:
   - timestamp
   - device public id + name
   - sensor_type
   - every top-level key from the `data` payload as its own column (later readings that introduce new keys simply add columns)

## Connecting a sheet (UI flow)

- Go to **Settings** in the IoT sidebar.
- Find the device row and click **Connect Google Sheet**.
- Either:
  - Paste an existing spreadsheet ID / URL, or
  - Click the "Create new sheet" link (opens a pre-titled sheet in a new tab) then paste the ID back.
- The UI will call the backend, store the mapping, and show the service-account email you must share with.
- Status changes to "connected".

You can connect the same sheet to multiple devices; rows from different devices are simply interleaved (the device columns make it easy to filter).

## Disconnecting

Click **Disconnect**. Future readings for that device will no longer be appended. Existing rows stay in the sheet.

## Row format (example)

| recorded_at          | device_id          | device_name          | sensor_type | temperature | humidity | lux  |
|----------------------|--------------------|----------------------|-------------|-------------|----------|------|
| 2026-06-09 14:31:02  | dev_abc123...      | Lab DHT22            | dht22       | 23.4        | 58       |      |
| 2026-06-09 14:31:05  | dev_abc123...      | Lab DHT22            | dht22       | 23.5        | 57       |      |
| 2026-06-09 14:31:10  | dev_def456...      | Window Light Sensor  | tsl2561     |             |          | 1240 |

Numeric vs string columns are preserved as far as Google Sheets allows; the service simply writes the JSON values it receives.

## Troubleshooting

- "Permission denied" or writes stop → re-share the sheet with the service account (Editor).
- New columns not appearing → the append code adds columns on the fly; very wide payloads may hit Google limits (rare in education use).
- Multiple devices writing to one sheet → perfectly supported; use the `device_name` or `device_id` column to filter or create views.

See also the [Workflows](../workflows/index.md) section if you need more sophisticated transformations before the data lands in Sheets.