# Timestamps and Ordering

Two different clocks are involved in every reading:

- The device's idea of "when this measurement was taken" (`recorded_at` supplied by the device in the ingest body, or omitted).
- The server's idea of "when we received and stored the row" (the `created_at` / `updated_at` timestamps on the `iot_device_data` row, plus the `recorded_at` defaulting to `now()` when the device does not supply one).

## How `recorded_at` is handled on ingest

```php
'recorded_at' => $validated['recorded_at'] ?? now(),
```

- If the device sends a valid ISO-8601 (or anything Carbon can parse) in the `recorded_at` field, that value is stored verbatim.
- If omitted, the server uses the current time at the moment the request is processed.
- Devices that buffer readings while offline or that have a good RTC are encouraged to send their own timestamps so that charts show the true time of the event rather than the (possibly much later) time the device was able to deliver the data.

## Ordering in queries

- The Data Explorer and widget data queries almost always `ORDER BY recorded_at DESC`.
- This means that if a device sends a burst of old buffered readings with correct past timestamps, they will appear in the correct historical position even if they arrived out of order.
- The `id` column is still monotonically increasing and is a safe tie-breaker when two readings have identical `recorded_at` values (rare).

## "Latest per sensor" helper

The `latestData` controller method returns the most recent reading (by `recorded_at`) for each distinct `sensor_type` a device has ever used. It is used by some dashboard widgets and by external scripts that only care about "current state".

## Clock skew and classroom reality

Student devices often have terrible clocks (no battery-backed RTC, no NTP on first boot, etc.). The platform therefore:

- Always accepts whatever `recorded_at` the device claims (within reason).
- Also stores the server receive time implicitly via the row's `created_at`.
- The UI prefers `recorded_at` for human-facing charts because that is what the student usually means ("the temperature at 14:30 in the greenhouse").

If you see readings with timestamps in 1970 or in the far future, the device either has a completely uninitialised RTC or is sending Unix epoch seconds instead of ISO strings. The ingest accepts both; fix the firmware to emit proper ISO-8601 or let the server supply the time by omitting the field.