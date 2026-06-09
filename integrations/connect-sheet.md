# Connecting a Device to a Google Sheet

This is the most common "I want my data somewhere I can show my teacher / parents" workflow.

## Prerequisites

- A Google account that can create or edit spreadsheets.
- The device already registered in the IoT console (you need its id to attach the sheet).

## Steps

1. In the IoT console go to **Settings** (or open the device row menu on the Data page).
2. Find the device and click **Connect Google Sheet**.
3. You now have two choices:
   - **Paste an existing sheet** — copy the long ID from the URL (`.../spreadsheets/d/1a2b3c.../edit`) or the whole URL and paste it.
   - **Create a new sheet** — the UI gives you a link that opens Google Sheets with a sensible default title. Create it, then copy the ID back into the dialog.
4. Click **Connect**.
5. The platform responds with the email address of its Google Service Account (something like `semiblock-iot@...gserviceaccount.com`).
6. In Google Sheets, click **Share** → paste that email → give it **Editor** access → the platform can now append rows.

## What happens next

- Every successful `POST /iot/data` for that device will cause a background job to append one row.
- The row contains the timestamp, device identifiers, `sensor_type`, and every top-level key from the `data` payload as its own column.
- Columns are added on the fly when new keys appear.

## Multiple devices → one sheet

Perfectly supported. Rows are simply interleaved. Use the `device_name` or `device_id` column (or Google Sheets filters / QUERY function) to separate concerns.

## Changing the sheet later

You can disconnect the old sheet and connect a different one at any time. Historical rows already written stay in the old sheet; future readings go to the new one.

## Removing the integration

Click **Disconnect**. The mapping row is deleted. The service account share in Google can also be removed (the platform will simply stop trying to write).

See the parent [Google Sheets](../google-sheets.md) page for the exact row format and troubleshooting.