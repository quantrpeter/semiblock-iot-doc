# Workflow Actions

Actions are the nodes that actually *do* something when a workflow runs.

## Google Sheets append

- Target: a specific spreadsheet (chosen at workflow design time) or "the sheet connected to the triggering device".
- Row template: you map columns to expressions such as `reading.recorded_at`, `device.name`, `payload.temp`, literal strings, etc.
- The action uses the same service-account mechanism as the automatic per-device connection, so the sharing requirements are identical.

## Queue command for a device

- Target device: the triggering device, a specific other device, or a device chosen by expression.
- Command name (string) and payload (JSON template).
- The command is written to `device_commands` with status `pending`.
- The next time the target device calls `/iot/data` or `/iot/commands/poll` it will receive the command in the response and the row will be marked `delivered`.

This is the primary way the platform achieves a lightweight "cloud told the board to do X" story without the board having to maintain a persistent connection.

## HTTP / webhook call

- Method, URL, headers, and body template.
- Useful for:
  - Power Automate / Zapier / Make.com flows
  - Discord / Slack / email notifications via their incoming webhooks
  - Your own backend or another school's system
- Timeouts and retries are modest; a failing HTTP action is logged but does not roll back the reading or other actions in the same workflow run.

## Log / debug / notification (internal)

- Writes a structured entry to a workflow run log visible in the UI.
- Can also surface a transient toast in the web console for the owner if they happen to be looking at the time the workflow fires.

## "No-op" / pass-through

- Sometimes useful purely for structuring a complex graph or for temporarily disabling a branch during debugging.

## Error handling

Every action is executed inside a try/catch at the engine level. A single failing action (bad Sheets permission, unreachable webhook, etc.) is logged with full context and the workflow continues with the remaining actions. The original telemetry ingest is never affected.

## Data flow between actions

Later actions in the graph can reference values produced or extracted by earlier actions (for example, "take the temperature from the reading, convert to Fahrenheit in a 'Compute' node, then use the Fahrenheit value in the Sheets row and also in the notification text").

The exact variable scoping and expression language is defined by the WorkflowEngine implementation; the UI tries to make the available context obvious in the node inspectors.