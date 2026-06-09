# Device Never Appears Online / No Data Arrives

## Symptoms

- Device row stays "offline".
- `last_seen_at` is null or very old.
- No rows appear in the Data Explorer for this device.
- Dashboard widgets for the device show the placeholder "No numeric readings yet".

## Most common causes (check in this order)

1. **Wrong credentials in the firmware**  
   The #1 cause. Re-copy the Device ID and Secret from the web UI (use the key icon on the device row). Do not re-type.

2. **Wrong server URL**  
   `IOT_SERVER` (or equivalent) must point at the host that serves `/iot`, including scheme and port in local development. A very frequent mistake is leaving a production URL in code while testing locally, or vice-versa.

3. **Using the human name instead of the generated device_id**  
   The platform does not look up devices by the friendly name you typed when creating them. You must send the `dev_...` string.

4. **HTTP client not sending JSON correctly**  
   Some Arduino libraries or `curl` one-liners forget `Content-Type: application/json`. The Laravel validator then rejects the request (422) before it ever reaches the auth check.

5. **Device is sending to the wrong path**  
   It must be exactly `/iot/data` (or `/iot/commands/poll`). A trailing slash or `/api/iot/data` will 404 or be caught by the SPA catch-all and return HTML.

6. **TLS / certificate issues on the device**  
   Many classroom networks MITM HTTPS or the board's trust store is too old. Try plain `http://` to your dev machine first to rule this out.

7. **The reading is arriving but the numeric field you chose for the widget does not exist**  
   The widget may be configured for a field that this particular sensor_type has never emitted. Check the raw Data Explorer row — if the JSON is there, create or edit the widget to pick a key that actually exists in the payload.

## Diagnostic steps

- On the device, print the exact URL, headers, and body you are about to send, plus the HTTP status code and response body you received.
- In the server `storage/logs/laravel.log`, search for the timestamp of the attempt. 401s and 422s are logged with the reason.
- Temporarily use `curl` from your laptop (using the exact same strings) to prove the server side is willing to accept the credential.

Once the first successful 201 is logged, the device will flip to online and `last_seen_at` will be set. All subsequent problems are usually about the *content* of the data rather than reachability.