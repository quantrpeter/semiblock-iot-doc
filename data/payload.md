# sensor_type and the Free-Form Payload

The ingest API was intentionally designed with almost no schema so that students can evolve what they send without waiting for a backend change or a database migration.

## `sensor_type`

- Optional string.
- Conventionally a short lowercase identifier: `dht11`, `tsl2561`, `battery`, `gps`, `weather`, `imu`, etc.
- Used for:
  - Grouping readings in the Data Explorer and widget form.
  - Filtering queries (`?sensor_type=dht11`).
  - Labelling series in charts when you have multiple sensors on one device.
- If you omit it, the reading is still stored (with `sensor_type = null`). The dashboard treats "All sensor types" as the union of everything the device has ever reported.

## `data` (the payload)

- Must be a JSON object (not a bare number or string).
- Can contain any structure you like: numbers, strings, booleans, nested objects, arrays.
- Only **numeric leaves** are offered as plottable fields in the "Add chart" dialog. The platform walks the object and collects keys whose values are numbers (or numeric strings that parse cleanly).
- Example that yields three plottable fields:

  ```json
  {
    "sensor_type": "weather",
    "data": {
      "temp": 19.2,
      "humidity": 64,
      "wind": { "speed": 3.4, "dir": 180 }
    }
  }
  ```

  The discovered fields would be `temp`, `humidity`, `wind.speed` (the UI currently flattens one level for the dropdown; deeper nesting is stored but may need a custom widget or export to use).

## Why not enforce a schema?

- Educational devices change constantly (a student adds a new sensor mid-project).
- Different classes use completely different hardware.
- The cost of a migration or a "register sensor type" step would kill the "it just works" feeling that is the whole point of the platform.

The trade-off is that you can send nonsense and it will be stored. The UI tries to be helpful by only surfacing numeric fields it has actually seen recently.

## Evolving a payload safely

If you add a new key (`"lux": 1230`) to an existing sensor_type, old readings simply won't have that key. Widgets that plot the new field will have fewer points until enough new data arrives. Nothing breaks.

If you rename a key, both the old and new names will coexist in the discovered field list until the old data ages out or you stop querying it. You can keep a widget for the old name for historical comparison.

## Large or binary payloads

The `payload` column is a normal JSON column (or `jsonb` on Postgres). Multi-megabyte images or oscilloscope traces are not a good fit — store a URL or a hash instead and put the heavy blob somewhere else (S3, local disk, another service). The platform is optimised for the "dozens of small numeric readings per minute per device" use case common in education.