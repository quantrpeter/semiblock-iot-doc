# Device and Sensor Type Facets

The Data Explorer page needs to offer useful filter dropdowns without the user having to remember exact names or type them.

## How the facets are populated

On every call to `GET /iot/device-data?...` the controller also runs two small supporting queries (only for the current user):

1. All devices the user owns, ordered by name:

   ```sql
   SELECT id, name FROM iot_devices
   WHERE user_id = ? ORDER BY name
   ```

2. All distinct non-null `sensor_type` values that appear in any reading belonging to the user's devices:

   ```sql
   SELECT DISTINCT sensor_type
   FROM iot_device_data
   JOIN iot_devices ON ...
   WHERE iot_devices.user_id = ?
     AND sensor_type IS NOT NULL
   ORDER BY sensor_type
   ```

These two lists are returned in the response under `filters.devices` and `filters.sensor_types`.

## Why return them on every data request?

- The set of devices is small (a student rarely has more than a few dozen).
- The set of sensor types grows slowly and is useful context for the current filtered view.
- It saves the frontend from making a second round-trip just to populate dropdowns.
- When the user applies a device or sensor filter, the next request will naturally return an updated (and still correct) facet list.

## "All" / "Any" option

The UI always adds a synthetic "All devices" / "All sensor types" choice that simply omits the corresponding query constraint.

## Performance note

Both facet queries are indexed and cheap. Even with tens of thousands of readings the `DISTINCT` on sensor_type is fast because the cardinality is low (usually < 20 distinct strings per student account).

## Future

If the platform ever supports shared "class" or "group" views, these facet queries will be extended to the union of devices visible to the viewer. For now they are strictly per-owner.