# Testing and Manual Execution of Workflows

Because workflows run in the cloud in reaction to real device data (or on a schedule), having good testing tools is essential.

## Manual "Run now"

Every workflow has a **Run** / **Execute now** button in the list and in the editor.

- This bypasses all trigger filters and conditions.
- It executes the graph with a synthetic or last-known reading context (or with no reading context for workflows that are purely action-oriented).
- Perfect for verifying that a Google Sheet connection works, that a command is queued, or that an HTTP webhook receives the payload you expect.

## The workflow run log

After each execution (manual or triggered) the engine can record:

- Start time, duration, final status (success / partial / failed)
- Which trigger or manual invocation caused it
- Per-node inputs and outputs (or at least the ones that are cheap to serialise)
- Any exceptions with stack traces

The UI shows a list of recent runs for a workflow and lets you drill into a single run to see what happened.

## Using the Data Explorer + "Run now" together

A common debugging pattern:

1. Find an interesting historical reading in the Data Explorer.
2. Note its device and sensor_type.
3. Go to the workflow, set its trigger temporarily to that exact device + sensor_type (or just use manual run with a provided context).
4. Click Run now.
5. Watch the Sheets / command / log side effects appear.
6. Revert the trigger to the real desired configuration when you are happy.

## Dry-run / simulate mode (future)

A future enhancement may add a "dry run" that walks the graph, evaluates expressions, and shows what *would* have been sent to Sheets or queued as a command, without actually performing the side effects. Until then, the pragmatic approach is to connect a test sheet or a test device that you don't mind polluting, or to add logging actions that are easy to ignore.

## Common testing pitfalls

- Forgetting that the workflow must be **Active** even for manual runs in some implementations.
- Assuming the trigger condition will be evaluated on a manual run (it usually isn't — manual run is "force execute this graph").
- Not realising that a Google Sheets action will actually append a row on every test run (use a dedicated "test" sheet while iterating).

Good testing discipline here saves a lot of "why did my sheet get 17 extra rows" confusion later.