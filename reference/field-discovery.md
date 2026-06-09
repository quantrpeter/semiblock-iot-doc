# Payload Field Discovery

The "Add chart" form does not make the user type field names by hand. Instead it offers a dropdown of numeric keys that the platform has actually seen in recent readings for the chosen device.

## How it works (backend)

When the widget modal loads a device, it calls:

```
GET /iot/device/{id}/fields
```

The controller (`deviceFields`) does the following:

1. Takes the 50 most recent readings for the device (ordered by `recorded_at desc`).
2. For each reading, walks the `payload` JSON.
3. Collects every key whose value is numeric (int or float) or a numeric string.
4. Groups the keys by `sensor_type` (or `null` for readings without one).
5. Returns a structure like:

```json
{
  "sensors": [
    {
      "sensor_type": "dht11",
      "fields": ["temp", "hum"]
    },
    {
      "sensor_type": null,
      "fields": ["voltage", "rssi"]
    }
  ]
}
```

The frontend then:

- Populates the Sensor Type dropdown from the `sensor_type` values (plus the synthetic "All sensor types").
- When the user picks a sensor (or All), builds the union of matching fields for the Data Field dropdown.
- Keeps any previously selected field even if it is no longer in the current sample (so old widgets continue to work).

## Limitations & future improvements

- Only the most recent N readings are sampled. A field that appears only in very old data may not be offered until a new reading containing it arrives.
- Deeply nested objects are flattened only one level in the current implementation (`wind.speed` appears, but `imu.accel.x` may not).
- Non-numeric values (strings, booleans, objects, arrays) are never offered as chartable fields. You can still see them in the raw Data Explorer and in Google Sheets exports.

## Why this design?

It gives students an "it just works" experience: they push whatever JSON their sensor produces, then when they open the chart dialog the interesting numbers are already listed. No separate "register sensor schema" step is required.