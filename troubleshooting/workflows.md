# Workflows Not Firing or Producing Unexpected Results

## The reading arrived but the workflow did nothing

1. **Is the workflow Active?**  
   There is a toggle on the workflow list and in the editor. Inactive graphs are never evaluated.

2. **Does the trigger actually match?**  
   - Device selector: did you pick a specific device or "Any"?
   - sensor_type filter: does it exactly match what the device is sending (case, spelling, null vs. a value)?
   - Condition expression: is the comparison correct? (Many students write `temp > 30` when the key is actually `temperature` or nested under `payload`.)

3. **Was there an exception during evaluation?**  
   The ingest path wraps workflow execution:

   ```php
   try {
       app(WorkflowEngine::class)->runForReading($device, $reading);
   } catch (\Throwable $e) {
       Log::error('Workflow evaluation failed', [...]);
   }
   ```

   Look in `laravel.log` for the error. A bad expression, a missing node type, or an action that throws will be logged but will **not** drop the reading.

4. **Action side-effect failed silently?**  
   Google Sheets appends and HTTP calls from actions are also wrapped. Failures are logged per-device. The workflow run record (if the engine creates one) will usually show which node failed.

## Workflow runs but the wrong data ends up in the sheet / command

- Check the expression or template in the action node. The context variable names are usually `device`, `reading`, `payload`.
- For Sheets, remember that the row is built from the *current* reading's payload; if your device sometimes omits a key, the column will be empty for that row.
- For commands, verify that the device you targeted is the one that is actually polling. A command queued for device A will never be delivered to device B even if B is the one that triggered the workflow.

## Manual run works, automatic trigger does not

- The manual run bypasses the trigger filter. Re-check the trigger configuration (device, sensor_type, condition).
- Make sure the workflow was saved *after* you last edited the trigger.

## Performance / scale

A single very complex workflow with many nodes or heavy HTTP calls can slow down ingest for that device. The platform still accepts the reading; the workflow just takes longer. For classroom loads this is rarely a problem. If you see multi-second delays, simplify the graph or move heavy work to an asynchronous queue (future improvement).

## Debugging tips

- Add a "Debug Log" action node early in the graph; it will write the incoming context to the run log.
- Use the Data Explorer to confirm the exact `sensor_type` and payload shape that is arriving.
- Temporarily change the trigger to "Any device" + no condition so you can see whether the graph itself executes at all.