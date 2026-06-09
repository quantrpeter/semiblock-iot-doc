# Pushing Your First Reading

Once you have a device registered and its credentials in hand, the next step is to make it send data.

There are two recommended paths:

1. **Use the visual blocks inside a SemiBlock editor** (MicroPython, etc.) — zero HTTP boilerplate, works in the simulator too.
2. **Call the HTTP API directly** from any firmware or scripting language — maximum flexibility, same endpoint the blocks use under the hood.

## Path 1 — Visual blocks (fastest for students)

See the dedicated page:

- [Using the IoT blocks in the editor](using-blocks.md)

Typical flow inside the editor:

1. Drag `iotConnect` near the top of your program and fill in the three values (or the constants will be set for you by the generator in some editors).
2. After you read a sensor, drag `iotPushReading` (whole object) or `iotPushValue` (single key).
3. "Upload & Run" — the simulator will show the push in its cloud pane; a real board will perform the POST.

Within a few seconds the reading should appear in the **Data Explorer** and you can add a **Dashboard** widget for it.

## Path 2 — Direct HTTP / REST

See the full technical spec:

- [Ingest API Reference](ingest-api.md) (relative link works when the docs are viewed together)

Minimal `curl` example you can run from any machine that has the credentials:

```bash
curl -X POST https://your-host/iot/data \
  -H "Content-Type: application/json" \
  -d '{
    "device_id": "dev_abc...",
    "secret_key": "s3cr3t...",
    "sensor_type": "test",
    "data": { "value": 42 }
  }'
```

A successful response returns HTTP 201 and a JSON body containing the stored reading plus any pending commands.

## Verifying it worked

- Open the IoT console → **Data** (or the raw `/iot/device-data` table).
- You should see a new row with your `sensor_type` and the JSON you sent.
- Go to **Dashboard** → **+ Add chart**, select the device, and plot the field(s) you just sent.

If nothing appears, see the [troubleshooting section](../troubleshooting/credentials.md) (most 401s are credential typos or wrong host).