# Workflows (Automations)

Workflows let you attach behaviour that runs in the cloud whenever a device reports a new reading (or on demand).

See the sibling documentation for:

- [Creating and editing node graphs](creating.md)
- [Triggers that fire on telemetry](triggers.md)
- [Available actions (Sheets, device commands, HTTP, …)](actions.md)
- [Manual execution and testing](testing.md)

Workflows are evaluated inside the ingest path (wrapped so a broken workflow never drops a reading) and can also be invoked manually from the UI or via the run API.