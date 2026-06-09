# Creating and Editing Workflows

Workflows are visual node graphs that live in the cloud and can react to device data or be run manually.

## Opening the editor

From the IoT sidebar choose **Workflows** → **+ New workflow** (or edit an existing one). This opens a full-screen node-based editor (similar in spirit to Node-RED or the visual editors you already know).

## Basic structure

A workflow always has:

- One or more **trigger** nodes (what causes this graph to run).
- Zero or more **action** nodes (what happens when it runs).
- Connections (wires) that define execution order and data flow.

## Adding a reading trigger (the most common case)

1. Drag a **"Device Reading"** trigger onto the canvas.
2. Configure it:
   - Choose a specific device, or "Any of my devices".
   - Optionally filter by `sensor_type`.
   - Optionally add a simple condition (e.g. `payload.temp > 30`).
3. When a matching reading arrives at `/iot/data`, the platform will start executing this workflow with the reading (and its device) as context.

## Adding actions

Common action nodes include:

- **Append to Google Sheet** — writes a row (you choose which sheet or use the per-device connection).
- **Queue command for device** — the next time the target device calls the ingest/poll endpoint it will receive this command + payload.
- **HTTP request** — call an external API (Power Automate, Discord webhook, your own server, …).
- **Log / Debug** — writes a line to the workflow run log (great while developing).

## Variables and the reading context

When a reading trigger fires, the workflow engine makes several pieces of data available to downstream nodes:

- `device` (id, name, device_id, type, last_seen, …)
- `reading` (id, sensor_type, payload, recorded_at)
- `payload` (shortcut to `reading.payload`)
- Any values you extract in earlier nodes can be referenced by name in later nodes.

Most nodes have an expression editor or simple template language so you can build the sheet row, the command payload, or the HTTP body dynamically from the incoming data.

## Saving and activating

- The graph is saved continuously or on an explicit Save button.
- There is an **Active** toggle. Only active workflows are evaluated on ingest.
- You can have many workflows; they are evaluated independently (a faulty one is caught so it cannot break telemetry for the device).

## Manual runs

From the workflow list or the editor you can click **Run manually**. This is extremely useful for testing actions without having to wait for a real device to report.

See the sibling pages for details on [triggers](triggers.md), [actions](actions.md), and [testing](testing.md).