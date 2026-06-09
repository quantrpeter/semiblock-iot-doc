# Selecting Device, Sensor Type, and Field

The widget form is deliberately "discovery driven" so students do not have to remember or type the exact JSON keys their firmware is emitting.

## Device picker

A simple `<select>` populated with all devices the current user owns (by name). Changing the device immediately triggers a call to the field-discovery endpoint and repopulates the sensor and field dropdowns.

## Sensor type dropdown

- Always contains the synthetic option **"All sensor types"**.
- Then one entry for every distinct `sensor_type` value that has appeared in any reading for the chosen device (including `null` / "no sensor type" if the device ever sent readings without one).

Choosing "All" means the widget will consider readings from every sensor the device has ever used when looking for the chosen field.

## Data field dropdown

- Populated from the numeric keys discovered in the payloads of recent readings for the selected sensor type(s).
- If you previously had a field selected and then change the device or sensor, the form tries to keep the old field name selectable even if it is not in the current sample (so existing widgets do not break when you edit them).
- If no numeric fields have been seen yet, a hint appears: "No numeric readings yet for this field. Send some readings from this device first."

## Why only numeric fields?

Charts need numbers. Strings, booleans, and objects are perfectly valid in payloads and will appear in the raw Data Explorer and in Google Sheets exports, but they are not offered as plottable series here. If you need to visualise a categorical or boolean value, consider pushing a derived number (0/1) or use a doughnut widget with multiple keys.

## "The field I want is not in the list"

- The device has not yet sent a reading containing that key under the chosen sensor type (or at all).
- The key exists but its value was never numeric in the sampled readings.
- The key is deeply nested and the current discovery walker only flattens one level.

In all cases the pragmatic fix is the same: push at least one reading that contains the desired numeric value, then re-open the widget form (or refresh the device in an existing edit). The new key will appear.