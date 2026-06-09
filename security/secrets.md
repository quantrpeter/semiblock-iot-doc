# Keeping Your Secret Key Safe

The `secret_key` generated for each device is a bearer credential. Anyone who possesses both the `device_id` and the `secret_key` can:

- Push arbitrary readings that will be stored under that device.
- Receive any commands that workflows have queued for the device.
- (Indirectly) cause workflows to run and Google Sheets to be written to.

## Best practices for students and classrooms

1. **Never commit the secret to a public Git repository.** If you must store it in a file that travels with the firmware, put it in a file that is `.gitignore`d and document that students must create their own `secrets.py` (or equivalent) locally.
2. **Rotate on suspicion.** The device edit screen has a "Regenerate secret" action (or simply delete the device and create a new one). All previous pushes with the old secret will of course remain in the database.
3. **Treat the secret like a password, not an API key you can publish.** The ingest endpoint has no rate-limit per secret in the current design — a leaked secret can be used to flood your own data (or, if workflows are attached, to trigger side effects).
4. **Use the copy buttons in the UI.** The credentials modal and the device list have one-click copy so students are not tempted to photograph or hand-type the 48-character secret.
5. **For shared hardware** (a club robot, a classroom sensor box) keep the secret on a need-to-know basis. The person who registered the device can always reveal it again from the web UI.

## What the platform does to help

- Secrets are only ever shown in full at creation time and via an explicit "reveal credentials" action.
- All other APIs that return device rows deliberately mask or omit the secret.
- The ingest path uses constant-time comparison (`hash_equals`) so timing attacks are not useful.
- Every successful ingest and poll updates `last_seen_at`, giving you an audit trail of when the credential was last used.

If you ever see readings you did not expect, regenerate the secret immediately and investigate which of your projects or students might have copied it.