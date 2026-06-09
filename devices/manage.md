# Editing, Deleting, and General Device Management

## Editing a device

From the Devices list or the device detail view you can change:

- Friendly **name** (highly recommended to keep meaningful)
- **Type** (the hardware category badge)
- **Description** (free text — location, student owner, notes, calibration info, etc.)

These fields are only visible to the owner via the authenticated web UI. They are never sent to or required from the device itself.

## Regenerating the secret

See the dedicated [credentials](credentials.md) page. The action is available on both the list (via the key icon) and the edit form.

## Deleting a device

- Deletes the `iot_devices` row.
- Does **not** delete historical `iot_device_data` rows (they remain useful for the Data Explorer, any connected Google Sheets, and audit).
- Does **not** automatically delete widgets that referenced the device (those widgets will show a "device not found" or "not connected" state until you edit or delete them).
- Does **not** delete workflows; workflow triggers that pointed at the device will simply never fire again.
- Any pending commands for the device are left in the table (harmless).

After deletion, any future ingest attempt using the old `device_id` will receive 401 (device not found).

## Bulk operations (future)

Today you must manage devices one at a time. Future UI may add multi-select delete, export of device lists, or "duplicate this device configuration for the next board".

## Ownership transfer

There is currently no "transfer ownership" feature. If a student graduates and you need their devices to belong to a teacher account, the pragmatic path is:

1. Teacher creates new replacement devices.
2. Update the firmware on the physical boards with the new credentials.
3. Optionally export the old readings (via Data Explorer or the connected Sheet) and note that they belong to the retired student account.

A proper transfer feature may be added later if classroom management demand justifies it.