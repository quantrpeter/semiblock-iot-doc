# Table of Contents

> Documentation for the **SemiBlock IoT Platform** — secure device management, telemetry ingestion, live dashboards, visual workflows, and Google Sheets integration that works with every SemiBlock visual editor (MicroPython, Java, JVM, and more).

## Getting Started

- [Overview](overview.md)
- [What is the SemiBlock IoT Platform?](getting-started/what-is-iot-platform.md)
- [Accessing the IoT Console](getting-started/access.md)
- [Your First Device](getting-started/first-device.md)
  - [Adding a device](getting-started/add-device.md)
  - [Device ID & Secret Key](getting-started/credentials.md)
- [Pushing Your First Reading](getting-started/first-reading.md)
  - [From the visual editor (recommended)](getting-started/using-blocks.md)
  - [Direct HTTP from any hardware or script](getting-started/http-api.md)

## Devices

- [Managing Devices](devices/index.md)
  - [Supported hardware types](devices/supported-types.md)
  - [Viewing, copying, and rotating credentials](devices/credentials.md)
  - [Edit, delete, and status](devices/manage.md)
  - [Online / offline and last-seen tracking](devices/status.md)

## Telemetry & Data

- [How Data Flows](data/index.md)
- [Ingest API Reference](data/ingest-api.md)
  - [Authentication with device credentials](data/auth.md)
  - [sensor_type and free-form JSON payload](data/payload.md)
  - [Commands returned in the response](data/commands.md)
- [Visual Blocks Integration](data/blocks.md)
  - [`iotConnect`, `iotPushReading`, `iotPushValue`](data/blocks.md)
- [Timestamps, ordering, and latest values](data/timestamps.md)
- [Querying readings (owner API)](data/query-api.md)

## Dashboard & Visualization

- [The Personal IoT Dashboard](dashboard/index.md)
- [Widgets & Charts](dashboard/widgets.md)
  - [Chart types (line, timeseries, bar, doughnut)](dashboard/chart-types.md)
  - [Picking device, sensor type, and numeric field](dashboard/fields.md)
  - [Sizing, colors, and reordering widgets](dashboard/layout.md)
- [Live data and refresh behavior](dashboard/live.md)

## Data Explorer

- [All Readings Across Your Devices](data-explorer/index.md)
  - [Search, filters, sorting, and pagination](data-explorer/filtering.md)
  - [Device and sensor type facets](data-explorer/facets.md)

## Automations

- [Workflows Overview](workflows/index.md)
- [Building node-graph automations](workflows/creating.md)
- [Reacting to incoming readings](workflows/triggers.md)
- [Actions: Sheets, commands, notifications](workflows/actions.md)
- [Testing and manual runs](workflows/testing.md)

## Integrations

- [Google Sheets](integrations/google-sheets.md)
  - [Connecting a device to a spreadsheet](integrations/connect-sheet.md)
  - [Service account & sharing requirements](integrations/service-account.md)
  - [Row format and auto-append behavior](integrations/append-format.md)
- [Sending Commands to Devices](integrations/commands.md)
- [External services via Workflows](integrations/external.md)

## Security & Operations

- [Keeping secrets safe](security/secrets.md)
- [Device authentication model](security/auth-model.md)
- [Status, heartbeats, and offline detection](security/heartbeats.md)

## Troubleshooting

- [Device never appears online](troubleshooting/offline.md)
- [Readings not showing in dashboard or sheets](troubleshooting/no-data.md)
- [401 Unauthorized from device code](troubleshooting/credentials.md)
- [Google Sheets writes failing](troubleshooting/sheets.md)
- [Workflows not firing](troubleshooting/workflows.md)

## Reference & Appendices

- [Complete Ingest API examples](reference/examples.md)
- [Payload field discovery](reference/field-discovery.md)
- [Changelog](reference/changelog.md)
- [Glossary](reference/glossary.md)