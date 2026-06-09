# Readings Not Showing in Dashboard or Sheets

The device is online (last_seen updates) but charts stay empty or Google Sheets never receives rows.

## Dashboard widgets empty

Possible causes:

- The widget is configured for a `sensor_type` or `field` that this device has never actually emitted under that name.
  - Solution: open the raw **Data** page, find a real row for the device, look at the JSON keys, then edit the widget and pick one of those keys.
- `max_points` is set too low or the time range filter in the backend query is excluding everything (rare — the widget query just takes the N most recent).
- The readings exist but all the values for the chosen field are non-numeric (strings, nulls, objects). Only numbers are plotted.

## Google Sheets not receiving anything

Checklist:

1. Is the device **connected** to a sheet in Settings? (The UI shows a status pill.)
2. Did you actually **share** the spreadsheet with the service-account email the platform gave you, and give **Editor** rights?
3. Is the service account still shared? (Someone may have removed it later.)
4. Are new readings still arriving at all? (Check the Data Explorer — if nothing is there, the problem is upstream, not Sheets.)
5. Did a workflow or ingest error occur? The append is wrapped in a try/catch that logs to `laravel.log`; a bad service-account credential or a sheet that was deleted will appear there.

## "It worked yesterday"

- The sheet was un-shared or deleted.
- The device secret was rotated (old pushes are fine; new ones use the new secret and the mapping is by device id, so it should continue — but re-check the connection status).
- A classroom Google Workspace admin tightened domain sharing rules and the service account is now blocked.

## Quick test

Manually trigger a push (use the HTTP example from the docs or just hit the ingest endpoint with curl using the device's current credentials). Then:

- Refresh the Data Explorer — do you see the new row?
- If yes but Sheets is still silent → the problem is the Sheets integration specifically.
- If no row at all → the device is not successfully reaching the ingest endpoint (see the offline troubleshooting page).

## Still stuck?

Look at the server logs around the ingest timestamp. The `GoogleSheetsService::appendReadingIfConnected` method logs every failure at error level with the device id and the exception message. That message is usually enough to diagnose expired credentials, deleted sheets, or permission problems.