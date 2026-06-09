# Ingest API (Device → Cloud)

This is the only endpoint that devices (or scripts) are expected to call. It is deliberately minimal and does not require a user session — only the per-device secret.

## Endpoint

```
POST /iot/data
```

**Authentication** (choose one):

- JSON body fields: `device_id` + `secret_key`
- HTTP headers: `X-Device-Id` and `X-Device-Secret`

Both are accepted; headers take precedence if present.

## Request body

```json
{
  "sensor_type": "dht11",
  "data": {
    "temperature": 24.7,
    "humidity": 61
  },
  "recorded_at": "2026-06-09T14:22:05Z"   // optional, server uses now() if absent
}
```

- `sensor_type` — string, free form, used for grouping and filtering (e.g. `dht11`, `battery`, `gps`, `weather`). Can be omitted for single-purpose devices.
- `data` — **required** object. Any JSON. The dashboard later discovers numeric leaves for charting.
- `recorded_at` — optional ISO-8601 timestamp. Useful when the device buffers readings while offline.

## Successful response (201)

```json
{
  "data": { /* the stored IotDeviceData row */ },
  "commands": [
    { "id": 42, "command": "setLed", "payload": { "r": 255, "g": 0, "b": 128 } }
  ]
}
```

- `commands` — any pending commands that a workflow or the UI has queued for this device. After being returned they are marked `delivered`. Devices that only need to receive commands (without sending data) can call the lighter `POST /iot/commands/poll` endpoint instead.

## Error responses

- 401 — missing or invalid `device_id` / `secret_key`
- 422 — malformed JSON or missing `data` field

## Example using MicroPython + urequests (what the blocks generate)

```python
import urequests, json

IOT_SERVER   = "https://your-semiblock-host"
IOT_DEVICE_ID = "dev_xxxxxxxxxxxxxxxxxxxx"
IOT_SECRET    = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

def push(sensor, payload):
    urequests.post(
        IOT_SERVER + "/iot/data",
        json={
            "device_id": IOT_DEVICE_ID,
            "secret_key": IOT_SECRET,
            "sensor_type": sensor,
            "data": payload
        }
    )
```

See the [block documentation](blocks.md) and the [MicroPython project examples](../../semiblock-micropython-doc/projects/weather-station.md) for complete sketches.

## Security notes

- The secret is a bearer token. Treat it like a password.
- The endpoint is intentionally **not** behind the normal user auth middleware so that tiny devices with no cookie or OAuth support can still use it.
- All other IoT APIs (`/iot/device/*`, `/iot/dashboard/*`, etc.) **do** require a logged-in user session and will only return resources owned by that user.