# Authentication for the Ingest API

The two device endpoints (`/iot/data` and `/iot/commands/poll`) are deliberately **outside** the normal Laravel session / Sanctum / JWT auth system. They use a simple per-device bearer credential so that tiny microcontrollers with no TLS client certificate support, no cookie jars, and no OAuth libraries can still participate.

## The two credential values

- `device_id` (public, e.g. `dev_abc123...`)
- `secret_key` (long random secret, never shown in the UI after creation except on explicit reveal)

## Three ways to supply them (all accepted)

1. **JSON body** (most common from the visual blocks and Python/JS):

   ```json
   { "device_id": "...", "secret_key": "...", "sensor_type": "...", "data": {...} }
   ```

2. **HTTP headers** (clean for Arduino, ESP-IDF, curl one-liners):

   ```
   X-Device-Id: dev_...
   X-Device-Secret: s3cr3t...
   ```

3. **Mixed** — headers take precedence if both are present.

## Server-side check (from `IotDeviceController::authenticateDevice`)

```php
$deviceId  = $request->header('X-Device-Id', $request->input('device_id'));
$secretKey = $request->header('X-Device-Secret', $request->input('secret_key'));

$device = IotDevice::where('device_id', $deviceId)->first();

if (!$device || !hash_equals((string)$device->secret_key, (string)$secretKey)) {
    return null; // → 401
}
return $device;
```

Constant-time comparison is used so that timing attacks give no information.

## What this means for security

- If an attacker obtains both values they can impersonate the device forever (until you rotate the secret or delete the device).
- The values are therefore **not** suitable for multi-tenant "public" devices where the end user must not be able to extract the secret from the firmware.
- For classroom and club use this model is perfect: the secret lives in the firmware image or a local `secrets.py` that the student is trusted not to publish.

## Contrast with the rest of the IoT APIs

All the *owner* APIs (`/iot/device/*`, `/iot/dashboard/*`, `/iot/workflows`, the Data Explorer, etc.) go through the normal `AuthCheck` middleware and a user session. They never accept device credentials. This separation keeps the attack surface for a leaked device secret limited to "only this one device's data and commands".