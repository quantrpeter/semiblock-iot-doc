# Adding a Device

Every physical board or gateway that will send data must first be registered in the IoT console. Registration gives you the two values your code will use to authenticate: `device_id` and `secret_key`.

## Step-by-step

1. Log into the main SemiBlock site.
2. Navigate to **/iot** (or click the IoT icon in the main navigation).
3. In the left sidebar choose **Devices**.
4. Click the big **+ Add Device** button.
5. Fill in:
   - **Name** (required) — something you will recognize later, e.g. "Classroom Weather Station #3".
   - **Type** (optional but recommended) — pick from ESP32, ESP8266, Arduino Uno/Nano, Raspberry Pi, micro:bit, or "Other".
   - **Description** (optional) — free text for notes, location, student name, etc.
6. Click **Create**. The platform immediately generates a cryptographically random `device_id` (prefixed `dev_`) and a 48-character `secret_key`.
7. The credentials are shown in a modal. **Copy them now** — the secret is never shown in plain text again (you can always reveal it later via the "key" icon on the device row).

## After creation

- The device appears in the list with status **offline**.
- Status will change to **online** the first time it successfully authenticates (either by pushing data or by polling commands).
- `last_seen_at` is updated on every successful authenticated call.

## Next

- [Understand what the credentials are for](credentials.md)
- [Push your first reading from the visual editor or HTTP](first-reading.md)
- [Return to the Devices list any time to edit the name/description or delete the device](manage.md)