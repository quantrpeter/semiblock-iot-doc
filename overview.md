# SemiBlock IoT Platform

![IoT banner](img/overview/overview.png){width=100%}

The **SemiBlock IoT Platform** is the cloud layer that turns your visual programming projects into always-connected systems. It provides:

- **Device identity & secrets** — register any board or gateway and receive a unique `device_id` + `secret_key`.
- **Telemetry ingestion** — a simple, schema-less HTTP API (`POST /iot/data`) that accepts any sensor reading. Works from MicroPython `urequests`, Arduino HTTP, Python `requests`, or directly from the visual `iotPush*` blocks.
- **Live & historical dashboards** — drag-and-drop charts (line, timeseries, bar, doughnut) that automatically discover numeric fields from the data your devices actually send.
- **Visual workflows** — node-graph automations that can react to incoming data, write to Google Sheets, send commands back to devices, or trigger external services.
- **Google Sheets bridge** — one-click (or paste-ID) connection per device; new readings are appended automatically by a server-side service account.
- **Bidirectional commands** — devices can poll for pending commands produced by workflows or the UI.

Everything is designed to work seamlessly with the SemiBlock family of visual editors (MicroPython, Java, JVM, etc.) while remaining completely usable from plain firmware or scripts.

## Core Concepts

| Concept          | Description |
|------------------|-------------|
| **Device**       | A registered "thing" (ESP32, Arduino, Pi, micro:bit, custom gateway…). Owns a public `device_id` and a secret `secret_key`. |
| **Reading**      | One row in `iot_device_data`. Contains `sensor_type` (e.g. `dht11`, `weather`, `battery`) + free-form JSON `payload`. |
| **Widget**       | A dashboard chart bound to one device + (optionally) one sensor_type + one numeric field inside the payload. |
| **Workflow**     | A directed graph of nodes that can be triggered by a reading or manually. Can produce side-effects (Sheets, commands, HTTP calls). |
| **Google Sheet** | Optional per-device link. When present, every new reading is appended as a row. |

## How It Fits Together

1. You log into the main SemiBlock site and open **/iot**.
2. You create one or more Devices and copy the generated credentials.
3. In your visual project (or firmware) you call `iotConnect` (or set the three constants) and then `iotPushReading` / `iotPushValue`.
4. Data arrives at the platform, is stored, workflows are evaluated, and Google Sheets (if connected) is updated.
5. You open the IoT Dashboard, add charts, and watch live data. You can also browse the raw Data Explorer or build automations.

## Next Steps

- [Get started with your first device](getting-started/first-device.md)
- [Push data from the visual editor or via HTTP](getting-started/first-reading.md)
- [Build your first dashboard](dashboard/widgets.md)
- [Connect a Google Sheet](integrations/google-sheets.md)
- [Explore the Ingest API](data/ingest-api.md)

See the full [Table of Contents](toc.md) for every topic.
