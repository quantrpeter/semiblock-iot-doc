# Telemetry & Data

All data that arrives from devices is stored as individual readings and can be queried both by the visual dashboard and by the raw Data Explorer.

- [Ingest API (the wire protocol devices use)](ingest-api.md)
- [How the visual blocks generate the HTTP calls](blocks.md)
- [Timestamps and "latest per sensor" queries](timestamps.md)
- [Owner-side query APIs used by the Data page and widgets](query-api.md)

The platform is intentionally schema-less on the `payload` column so you can evolve what a sensor sends without migrations. The dashboard and field-discovery logic sample recent readings to present a useful UI even when the shape of the data changes over time.