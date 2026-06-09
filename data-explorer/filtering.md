# Search, Filter, Sort, and Pagination in the Data Explorer

The Data page is backed by a single powerful server-side query (`IotDeviceController::allData`) that scales to thousands of readings per student without sending everything to the browser.

## Search box

A single free-text input that matches (case-insensitive `LIKE`) against:

- Device friendly name
- `sensor_type`
- The stringified JSON `payload`

Example: typing `temp` will surface any reading whose payload contains a key or value with "temp" in it, plus any device whose name contains "temp".

## Device filter

A dropdown populated with all of the current user's devices (id + name). Selecting one adds `?device_id=...` and restricts the table to readings from only that device.

## Sensor type filter

A dropdown of all distinct `sensor_type` values the user has ever received (across all devices). Selecting one adds the filter server-side.

## Sort & direction

Clickable column headers (or a sort select + asc/desc toggle) let you order by:

- `recorded_at` (default, newest first)
- `sensor_type`
- `device_name` (joins to the device table)
- `id` (creation order in the database)

The allowed list is small and validated on the server so there is no SQL injection risk.

## Pagination

- Default page size 20, user can choose up to 100.
- Standard "1 … 7 8 9 … 42" pager.
- All filters and the current sort are preserved when changing pages (they live in the URL query string so deep links and browser back/forward work).

## Why server-side?

A classroom account can easily accumulate tens or hundreds of thousands of readings over a term. Sending the entire history to the browser would be slow and would make the UI unresponsive. By keeping search, filter, sort, and pagination on the server we only ever transfer one page of rows plus the small facet lists (devices and sensor types) needed to render the controls.

## Export

There is currently no one-click "download CSV" button on the Data page (you can use the connected Google Sheet for that, or call the same API from a small script and write your own CSV). A future iteration may add direct export for the currently filtered view.