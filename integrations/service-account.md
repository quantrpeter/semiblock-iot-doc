# Google Service Account Sharing Requirements

When you connect a device (or a workflow) to a Google Sheet, the platform does **not** use your personal Google credentials. Instead it uses a long-lived service account that is shared across all SemiBlock users.

## Why a service account?

- Students (especially younger ones) should not have to perform an OAuth consent flow or manage refresh tokens in firmware.
- The append happens asynchronously in the background after an ingest; there may not even be a browser session at that moment.
- A single service account can be granted access to thousands of student sheets without each student having to create their own GCP project.

## What you actually do

1. The IoT UI tells you the service account email (e.g. `semiblock-iot-prod@...gserviceaccount.com`).
2. You open the target Google Sheet.
3. Click **Share** (top right).
4. Paste the email address.
5. Change the role from "Viewer" to **Editor**.
6. (Optional but recommended) Uncheck "Notify people" if you don't want the service account's (non-existent) inbox to receive an email.
7. Click Send / Share.

From that moment the platform's background worker is allowed to append rows.

## What the service account can and cannot do

- **Can**: append rows to the specific sheets you shared with it.
- **Cannot**: read other sheets in your Drive, see your email, act as you in other Google services, or escalate its own privileges.
- If you later remove the share, the platform will start receiving permission errors on append attempts (logged, but the reading itself is safe).

## Classroom / domain considerations

Some school Google Workspaces have very restrictive sharing policies ("only people inside this domain", "must be pre-approved", "external users must be individually whitelisted").

If the service account address is from a different domain (or is a `gserviceaccount.com` address), you may need a Google Workspace admin to:

- Add the service account email to an "allowed external users" list, or
- Create a dedicated service account inside the school's own GCP project and have the SemiBlock deployment use that instead (ops / self-hosting task).

For personal Gmail / Google accounts the share usually "just works".

## Rotating or changing the service account

If the underlying GCP key or project ever changes, the email address will change. All previously connected sheets will need to be re-shared with the new address. The IoT UI will surface the new address after the change and existing connections will show a "needs re-share" warning until updated.

## Revoking access

Simply remove the share from the Google Sheet side, or click **Disconnect** in the IoT UI (or both). The platform will stop trying to write; existing rows remain.