# Viewing, Copying, and Rotating Credentials

After the initial creation modal, the secret is intentionally hidden. You can still retrieve it when you need to re-flash a board.

## From the Devices list

Each row has a small key icon (🔑). Clicking it opens a narrow dialog that shows:

- Device ID (always visible, safe to copy)
- Secret Key (revealed only while the dialog is open; big copy button)

The dialog also offers a **Regenerate** action that immediately replaces the secret with a new random value and shows it once (old value is instantly invalid).

## From the device detail / edit screen

The same reveal + regenerate controls are present on the full edit form.

## Why the extra clicks?

- Prevents accidental shoulder-surfing or copy-paste into a chat window.
- Makes it obvious when a student is "looking at the secret" (good for classroom supervision).
- The reveal action can be audited in future (the current implementation just returns it; a log entry could be added later).

## Best practice

- Only reveal the secret when you are physically at the board or in a secure terminal that will be used to flash it.
- After flashing, close the dialog.
- If you ever suspect the secret has leaked (unexpected data, a student admits they shared it), hit **Regenerate** immediately. All historical readings remain; only future ingest calls are affected.