# Example Payloads & Responses

## Minimal temperature reading (what a DHT block often produces)

**Request**

```json
POST /iot/data
{
  "device_id": "dev_abc123def456",
  "secret_key": "s3cr3t...",
  "sensor_type": "dht11",
  "data": { "temp": 23.4, "hum": 57 }
}
```

**Response (abridged)**

```json
{
  "data": {
    "id": 98765,
    "iot_device_id": 42,
    "sensor_type": "dht11",
    "payload": { "temp": 23.4, "hum": 57 },
    "recorded_at": "2026-06-09T14:31:02.000000Z"
  },
  "commands": []
}
```

## Battery + location (multiple values, no sensor_type)

Some devices only ever send one kind of reading and therefore omit `sensor_type`.

```json
{
  "device_id": "...",
  "secret_key": "...",
  "data": {
    "voltage": 3.87,
    "lat": 22.3193,
    "lon": 114.1694,
    "satellites": 7
  }
}
```

In the dashboard you would pick the device, leave sensor type as "All", and then choose `voltage`, `lat`, etc. as the field to plot.

## Command returned to the device

If a workflow decided to turn on an LED or request a GPS fix, the ingest response contains it:

```json
{
  "data": { ... },
  "commands": [
    { "id": 17, "command": "setRelay", "payload": { "channel": 2, "state": 1 } },
    { "id": 18, "command": "sampleGps", "payload": {} }
  ]
}
```

The device firmware is responsible for interpreting the `command` string and the free-form `payload`.

## Using headers instead of body credentials (Arduino / ESP-IDF style)

```http
POST /iot/data HTTP/1.1
Host: iot.semiblock.example
Content-Type: application/json
X-Device-Id: dev_abc123def456
X-Device-Secret: s3cr3t...
Content-Length: 68

{"sensor_type":"battery","data":{"v":3.81}}
```

The controller accepts either style (or a mixture). Headers win when both are present.