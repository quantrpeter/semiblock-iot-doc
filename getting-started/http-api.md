# Direct HTTP / REST from Any Device or Script

If you are not using a SemiBlock visual editor (or you are writing a custom gateway, Python script, Node-RED flow, etc.), you can talk to the platform with any HTTP client.

## The only endpoint you normally need

```
POST /iot/data
```

Full specification: [Ingest API](../data/ingest-api.md)

## Minimal working example (Python + requests)

```python
import requests, os
from datetime import datetime, timezone

BASE = os.environ.get("IOT_SERVER", "http://localhost:8000")
DEV  = os.environ["IOT_DEVICE_ID"]
SEC  = os.environ["IOT_SECRET"]

def push(sensor_type, payload, recorded_at=None):
    body = {
        "device_id": DEV,
        "secret_key": SEC,
        "sensor_type": sensor_type,
        "data": payload,
    }
    if recorded_at:
        body["recorded_at"] = recorded_at.isoformat()
    r = requests.post(f"{BASE}/iot/data", json=body, timeout=10)
    r.raise_for_status()
    print("OK", r.json())
    return r.json()

# usage
push("test", {"value": 42})
push("dht11", {"temp": 23.8, "hum": 61})
```

## Minimal example (Arduino / ESP32 / HTTPClient)

```cpp
#include <HTTPClient.h>
#include <ArduinoJson.h>

const char* IOT_SERVER   = "http://192.168.1.50:8000";
const char* DEVICE_ID    = "dev_...";
const char* SECRET       = "s3cr3t...";

void pushReading(const char* sensor, float temp, float hum) {
  if (WiFi.status() != WL_CONNECTED) return;

  HTTPClient http;
  http.begin(String(IOT_SERVER) + "/iot/data");
  http.addHeader("Content-Type", "application/json");

  StaticJsonDocument<256> doc;
  doc["device_id"]  = DEVICE_ID;
  doc["secret_key"] = SECRET;
  doc["sensor_type"] = sensor;

  JsonObject data = doc.createNestedObject("data");
  data["temp"] = temp;
  data["hum"]  = hum;

  String body;
  serializeJson(doc, body);

  int code = http.POST(body);
  if (code == 201) {
    // optionally parse commands from http.getString()
  }
  http.end();
}
```

## Using headers instead of body fields

Some environments make it cleaner to put credentials in headers:

```http
POST /iot/data HTTP/1.1
X-Device-Id: dev_...
X-Device-Secret: s3cr3t...
Content-Type: application/json

{"sensor_type":"battery","data":{"v":3.79}}
```

The server accepts either location (or both). Headers win when present.

## Commands in the response

The 201 reply always contains a `commands` array. Even if you only care about pushing data, you should at least log or ignore the array so that future workflows that want to command the device will work.

A device that *only* wants to receive commands (no telemetry) can call the lighter endpoint:

```
POST /iot/commands/poll
```

with the same credentials; it returns `{ "commands": [...] }` and updates `last_seen_at`.

## Error handling recommendations

- 401 → credentials wrong or device deleted/rotated. Surface a clear message and (optionally) a LED pattern.
- 422 → you sent invalid JSON or omitted the `data` object.
- Network / timeout → the device should keep its own buffer or just drop the reading; the platform is not a reliable message queue.

## Rate limits & backoff

There are currently no hard per-device rate limits in the ingest path (the goal is classroom simplicity). However, flooding the server with thousands of readings per second from a single device will still cause you pain (your own Data Explorer and Sheets will become noisy). Be nice.