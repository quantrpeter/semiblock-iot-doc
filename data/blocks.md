# Visual IoT Blocks (`iotConnect`, `iotPush*`)

All SemiBlock visual editors that target MicroPython (and eventually other languages) include a dedicated "IoT" or "Cloud" category containing three blocks:

## `iotConnect`

**Purpose:** Tell the runtime the endpoint and the credentials for this device.

**Parameters (fields):**
- Server URL (default `https://iot.semiblock.com` or whatever your deployment uses)
- Device ID
- Secret Key

**Generated code (MicroPython):**

```python
IOT_SERVER   = "https://..."
IOT_DEVICE_ID = "dev_..."
IOT_SECRET    = "xxxxxxxxxxxxxxxx..."
```

These three globals are then used by the push blocks. The block itself emits no executable statement at the top level (the assignment is hoisted by the generator).

## `iotPushReading`

**Purpose:** Push a single sensor reading under a `sensor_type`.

**Parameters:**
- sensor type (string literal or variable)
- data value (can be a number or a variable/expression that evaluates to a number or dict)

**Typical usage:** after reading a DHT sensor you drag an `iotPushReading` with sensor type `"dht11"` and the whole reading object or just the temperature.

**Generated code (simplified):**

```python
urequests.post(IOT_SERVER + "/iot/data", json={
    "device_id": IOT_DEVICE_ID,
    "secret_key": IOT_SECRET,
    "sensor_type": "dht11",
    "data": {"temp": temp, "hum": hum}
})
```

## `iotPushValue`

**Purpose:** Push a single keyed value (convenient when you only have one interesting number).

**Parameters:**
- sensor type
- key (string)
- value (number/expression)

**Generated code:**

```python
urequests.post(IOT_SERVER + "/iot/data", json={
    "device_id": IOT_DEVICE_ID,
    "secret_key": IOT_SECRET,
    "sensor_type": "battery",
    "data": {"voltage": v}
})
```

## Simulator support

In the browser simulator the three blocks are implemented by calling an internal `_iot_push(...)` helper that records the reading in the simulated cloud pane so you can develop the logic without a real board or network.

## See also

- The full [Ingest API](ingest-api.md) for the exact wire format.
- The [MicroPython documentation](../../semiblock-micropython-doc/toc.md) for the block appearance, tooltips, and example projects that use them (Weather Station, etc.).
- [Dashboard widgets](../dashboard/widgets.md) — once data is flowing you will usually want to plot the fields you just pushed.