# Dashboard Widgets & Charts

The IoT Dashboard is a personal, rearrangeable grid of charts. Each chart (widget) is independently configured and can point at any of your devices.

## Adding a chart

1. On the Dashboard click **+ Add chart**.
2. Choose a **title** (e.g. "Greenhouse Temperature").
3. Pick **chart type**:
   - Line (classic)
   - Timeseries (recommended when you have real timestamps; x-axis is linear time)
   - Bar
   - Doughnut (good for categorical / last-value distributions)
4. Select the **Device**.
5. The form fetches the device's recent readings and populates:
   - **Sensor type** dropdown (or "All sensor types")
   - **Data field** dropdown — only numeric keys discovered in the payloads are offered.
6. Pick a **colour**, **width** (1–4 columns), **height**, and **max points** (how many of the most recent readings to plot).
7. Save. The widget appears immediately and loads its data.

## Editing & deleting

Each widget card has a `⋯` menu (top right) with **Edit** and **Delete**.

Editing re-opens the same form pre-filled; changing the device or sensor will cause the field list to be re-fetched.

## How data is resolved for a widget

- The backend looks at up to `max_points` recent readings for the chosen device (optionally filtered by `sensor_type`).
- For each reading it extracts the value of the chosen `field` from the JSON `payload`.
- It builds two parallel arrays: `labels` (the `recorded_at` timestamps) and `values`.
- The frontend renders the appropriate Chart.js configuration (timeseries uses a linear time scale so gaps are visually correct).

If a reading's payload does not contain the chosen field, or the value is not numeric, it is simply omitted from that point.

## "No numeric readings yet"

You will see a placeholder until at least one reading containing the selected numeric field has arrived. This is normal the first time you add a chart for a brand-new device.

## Reordering

Drag the widget cards (or use the reorder API exposed in Settings) to change their visual order. The order is persisted per user.

## Performance notes

- Widgets are independent; loading one slow device does not block others.
- The "All sensor types" choice unions the numeric fields across every sensor the device has ever reported.
- Very high-cardinality payloads (hundreds of keys) are supported but the field discovery samples only recent readings for speed.

See [Chart types](chart-types.md) for visual examples of each style.