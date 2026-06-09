# Google Sheets Writes Failing

## Symptoms

- Device is happily pushing readings (Data Explorer shows new rows, `last_seen_at` updates).
- Dashboard widgets work.
- But the connected Google Sheet never gets new rows, or stopped getting them at some point.

## Diagnostic order

1. **Is the device still marked "connected" in Settings?**  
   If the UI now shows "not connected", someone (or a script) disconnected it. Re-connect and re-share.

2. **Is the service account still shared on the sheet with Editor rights?**  
   Go to the sheet → Share → check that the exact email the platform originally gave you is still present and has Editor (not Viewer or Commenter).

3. **Did the sheet itself move or get deleted?**  
   If the spreadsheet ID now points to nothing, the append will fail. Re-create or re-connect to a valid sheet.

4. **Check the server logs**  
   `storage/logs/laravel.log` around the time of the last ingest for the device. Look for lines from `GoogleSheetsService`. Typical messages:
   - "insufficient permissions" → share problem.
   - "not found" → sheet deleted or ID wrong.
   - "invalid_grant" or credential errors → the platform's Google service account key may have been rotated or the project disabled (ops issue).

5. **Rate / quota**  
   Google Sheets has write quotas. A device that pushes 10 times per second for hours can eventually hit them. The platform logs the error and continues; the reading is safe in the database even if the sheet append is dropped.

## Recovery

- Re-share the sheet with the service account.
- Optionally click "Disconnect" then "Connect" again in the IoT UI (this re-validates the mapping).
- If you want a clean sheet, create a new one, share it, and connect the device to the new ID. Old rows stay in the previous sheet.

## Prevention

- Document the service-account email somewhere the students can find it (classroom wiki, printed on the lab wall).
- Use a dedicated "lab data" Google account rather than a personal one if possible, so that sharing survives student graduations.
- For very high-volume devices, consider exporting via the Data Explorer API or a custom BigQuery / Postgres sink instead of (or in addition to) Sheets.