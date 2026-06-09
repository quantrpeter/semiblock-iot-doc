# Workflow Triggers

A trigger is what causes a workflow graph to start executing. The most common trigger in the IoT platform is "a new reading arrived".

## Device Reading trigger (primary)

Configuration options:

- **Device** — a specific device, or "Any device I own".
- **Sensor type** (optional) — only fire when the reading's `sensor_type` matches (exact string match, or null).
- **Condition** (optional) — a simple expression evaluated against the reading.

Example conditions (the exact syntax depends on the expression engine chosen in the implementation):

- `payload.temp > 30`
- `payload.humidity < 20 && device.name contains "greenhouse"`
- `payload.battery < 3.3`

When the condition is omitted, every matching reading fires the workflow.

## How the platform wires the trigger

On every successful ingest the controller does (after storing the reading):

```php
app(WorkflowEngine::class)->runForReading($device, $reading);
```

The engine finds all active workflows whose trigger nodes are interested in this device / sensor_type / condition, and executes them with the reading context.

## Other possible triggers (future or partial)

- **Manual / "Run now"** — always available from the UI regardless of trigger configuration. Extremely useful for testing.
- **Schedule / cron** — "run this workflow every 15 minutes" (useful for polling external APIs or producing summary reports).
- **Device went offline** — a synthetic trigger that fires when `last_seen_at` is older than a threshold (can be implemented by a background job scanning devices).
- **Webhook from external system** — an unauthenticated or signed HTTP endpoint that can start a workflow (similar to how GitHub webhooks start actions).

## Multiple triggers on one workflow

Some engines allow a graph to have several trigger nodes; any of them starting the execution is sufficient. The current SemiBlock implementation may start with a single primary trigger per workflow for simplicity; additional entry points can be simulated by having multiple workflows that all call a shared "sub-flow" action if that concept is added later.

## Trigger vs. action responsibilities

The trigger decides *whether* to run. The actions decide *what* to do with the data that caused the run. Keeping the two concerns separate makes it easy for a student to reuse the same action graph (e.g. "notify teacher + write to master log sheet") from several different device triggers.