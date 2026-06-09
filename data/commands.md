# Commands Returned to Devices

The ingest response (and the dedicated poll endpoint) can carry a list of pending commands that the server wants the device to execute.

## Why commands exist

Workflows (and in the future the UI) can decide "because this temperature just went over 30 °C, I want the board to turn on a fan" or "sample the GPS now". The device cannot be pushed to from the cloud (it may be behind NAT, asleep, on a metered connection), so the platform uses a pull model:

- Device calls `/iot/data` (or the lighter `/iot/commands/poll`).
- Server returns any queued commands in the same HTTP response.
- Device firmware interprets the `command` string + `payload` and acts.
- Server marks those commands `delivered`.

## Command shape

```json
{
  "id": 42,
  "command": "setLed",
  "payload": { "r": 255, "g": 128, "b": 0 }
}
```

- `id` — database primary key, useful for dedup if the device wants to be careful.
- `command` — free string chosen by the workflow author or the future "send command" UI.
- `payload` — any JSON the workflow author attached.

The device firmware is entirely responsible for the mapping from `command` name to action. There is no built-in command vocabulary.

## How a device receives them (MicroPython sketch)

```python
resp = urequests.post(IOT_SERVER + "/iot/data", json={...})
if resp.status_code == 201:
    for c in resp.json().get("commands", []):
        handle_command(c["command"], c.get("payload", {}))
```

A device that only wants to receive commands (no telemetry) can call:

```python
resp = urequests.post(IOT_SERVER + "/iot/commands/poll", json={
    "device_id": IOT_DEVICE_ID,
    "secret_key": IOT_SECRET
})
```

## Delivery guarantees

- Commands are only marked `delivered` after they have been successfully returned in an authenticated response.
- If the device never calls again, the commands stay `pending` forever (they will be returned on the next successful contact).
- There is no "at least once" retry from the server side beyond that; the device must initiate contact.
- Commands are returned in `id` order (oldest first).

## Use from workflows

When you build a workflow, one of the action nodes will be "Queue command for device". You choose the target device (or "the device that triggered this workflow"), the command name, and an optional payload object. The node simply creates a `DeviceCommand` row; the next time that device talks to the ingest or poll endpoint it will receive it.

## Security

Only the owner of the device (the user who created it) can cause commands to be queued for it via the workflow UI or API. A leaked device secret lets an attacker *receive* commands that were already queued, but not create new ones (command creation goes through the authenticated user session, not the device credential).