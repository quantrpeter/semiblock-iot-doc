# 401 Unauthorized from Device Code

This is the most common first-time integration problem.

## Symptoms

- Device prints "401", "Invalid device credentials", or the request simply fails.
- In the web UI the device stays "offline" forever.
- `last_seen_at` never updates.

## Checklist (in order)

1. **Are you using the exact strings?**  
   Copy-paste from the credentials modal or the "key" icon on the device row. Do not re-type.

2. **Are you sending both values?**  
   The platform requires *both* `device_id` **and** `secret_key`. Sending only one produces 401.

3. **Header vs body?**  
   The examples in the docs show both styles. If your HTTP client library lower-cases header names, use the body fields instead (`device_id` / `secret_key` in the JSON).

4. **Did you regenerate or delete the device?**  
   Any previous secret is immediately invalid. Create a fresh device (or regenerate) and update the constant in your firmware.

5. **Are you hitting the right host?**  
   The `IOT_SERVER` constant must point at the same origin that serves the `/iot` console (including scheme and port in local dev). A common mistake is leaving `https://iot.semiblock.com` in code while testing against `http://localhost:8000`.

6. **Is the device row actually owned by the logged-in user who is looking at the dashboard?**  
   Cross-account mistakes are rare but possible in shared classroom logins.

## Still failing?

Enable verbose logging on the device side (print the exact JSON you are about to send and the response body). Then look at the Laravel logs (`storage/logs/laravel.log`) around the ingest timestamp — the controller logs the reason for 401s at debug level.

If the secret in the database and the secret you are sending are byte-for-byte identical but you still get 401, check that your HTTP client is not URL-encoding or truncating the (long) secret.