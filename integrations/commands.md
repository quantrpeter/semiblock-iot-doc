# Sending Commands to Devices (Bidirectional)

The platform supports a lightweight "cloud tells device to do something" channel without requiring the device to maintain a persistent connection.

## The pull model

1. A workflow (or future UI action) creates a `DeviceCommand` row with status `pending`, a `command` string, and a free-form `payload`.
2. The device, on its next contact with the platform (either a normal data ingest or an explicit poll), receives the pending commands in the HTTP response.
3. The server marks those commands `delivered` (with a timestamp).
4. The device firmware interprets the command name and payload and acts (turns on a relay, samples a sensor, reboots, etc.).

## Device side (sketch)

```python
# after a normal push or on a timer
resp = urequests.post(IOT_SERVER + "/iot/commands/poll", json={
    "device_id": IOT_DEVICE_ID,
    "secret_key": IOT_SECRET
})
for c in resp.json().get("commands", []):
    if c["command"] == "setRelay":
        do_set_relay(c["payload"])
    elif c["command"] == "sampleGps":
        reading = read_gps()
        # optionally push the result back as a normal telemetry reading
```

## How commands are created today

- Primarily from **Workflow** action nodes ("Queue command for device").
- The workflow author chooses the target device (or "the device that triggered me"), the command name, and builds a payload from the reading context or literals.
- In the future a manual "Send command" button may appear on the Devices or Dashboard UI.

## Delivery timing

- Best case: the device is already in a loop that calls the ingest or poll endpoint every few seconds → command latency is roughly one heartbeat.
- Worst case (device only wakes once per hour to save power): the command sits in `pending` until the next wake-up.
- There is no server-side push or SMS fallback; the device must initiate contact.

## Idempotency and ordering

- Commands are delivered in ascending `id` order.
- The device receives the database id; a careful firmware can remember the last id it acted on and ignore duplicates if the same command is delivered twice (e.g. because the device crashed between receiving and acting).
- The server does not guarantee "exactly once"; it guarantees "at least the commands that were pending at the moment of the device's call will be returned, then marked delivered".

## Security

- Only the owner of a device (via a logged-in session + workflow) can queue commands for it.
- A leaked device secret lets an attacker *receive* already-queued commands, but not create new ones.
- Commands are stored in plain text in the database; do not put secrets in command payloads.

## Use cases

- Turn on a fan or pump when a temperature/humidity threshold is crossed.
- Ask a mobile robot to return to base or take a high-resolution photo.
- Force a remote sensor to increase its reporting rate for a while ("burst mode").
- Reboot or enter a safe mode when a watchdog workflow detects the device has gone quiet.

See [Workflows](../workflows/index.md) for how to build the graphs that produce these commands.