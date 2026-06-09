# Calling External Services from Workflows

One of the most powerful features of the workflow system is the ability to reach out to almost any HTTP service when a device reports something interesting.

## The HTTP Request action node

Typical configuration:

- Method: `POST`, `GET`, `PUT`, etc.
- URL: can be a literal or an expression (e.g. `https://hooks.slack.com/services/...` or a URL built from the reading).
- Headers: static map or dynamic (Authorization tokens, Content-Type, etc.).
- Body: JSON template, form data, or plain text. You can reference `device`, `reading`, `payload`, and any values computed by earlier nodes in the graph.

## Common integrations

- **Discord / Slack incoming webhooks** — post a nicely formatted message when a sensor goes out of range.
- **Microsoft Power Automate / Logic Apps** — trigger a flow that sends email, creates a Planner task, or updates a SharePoint list.
- **Zapier / Make.com / n8n** — anything those platforms can do can be started from a simple POST.
- **Your own backend or another school's API** — for cross-project data sharing or leaderboards.
- **Push notification services** (Pushover, Pusher Beams, Firebase Cloud Messaging, …) — alert a phone when the greenhouse hits 35 °C.

## Example: Discord webhook from a high-temperature reading

Trigger: Device Reading, sensor_type = `dht11`, condition `payload.temp > 32`

Action: HTTP Request

```
POST https://discord.com/api/webhooks/123/abc
Content-Type: application/json

{
  "content": "🔥 Greenhouse is hot! {{payload.temp}}°C on {{device.name}} at {{reading.recorded_at}}"
}
```

## Error handling & retries

- Timeouts are modest (a few seconds).
- A failing HTTP action is logged with the response status/body if available.
- The workflow continues with later actions; one bad notification does not prevent a Google Sheet append or a command being queued for the device.
- There is no automatic exponential backoff or dead-letter queue in the first implementation; if you need reliability, make the external system idempotent and be prepared to re-run the workflow manually if you see a failure in the run log.

## Security considerations

- Any secrets (API keys, webhook tokens) you put into the action configuration are stored in the workflow definition (JSON in the database). They are only visible to the workflow owner via the authenticated UI.
- Do not put highly sensitive long-lived credentials into workflows that many students can edit.
- Prefer short-lived or narrowly-scoped tokens when possible.
- The platform's outbound IP addresses are stable; if the target service requires IP allow-listing, you can add them.

## Alternatives when HTTP is not enough

- For very high reliability or complex auth (OAuth client credentials, mTLS, etc.) you may want to write a small bridge service that the workflow calls, and have the bridge do the heavy lifting.
- For true pub/sub or streaming telemetry, the current platform is not the right layer; consider a local MQTT broker or a dedicated IoT hub.

The HTTP action node covers the vast majority of "tell someone or something else what just happened" classroom scenarios.