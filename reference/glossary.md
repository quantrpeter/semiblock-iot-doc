# Glossary

**Device**  
A registered "thing" in the IoT platform. Has a friendly name, a generated public `device_id`, a secret `secret_key`, an optional type (ESP32, Arduino, …), and timestamps for creation and last contact.

**Reading / Telemetry**  
One row stored in `iot_device_data`. Contains the owning device, an optional `sensor_type`, a free-form JSON `payload`, and the `recorded_at` time the device claims the measurement was taken.

**Widget**  
A single chart on a user's personal IoT Dashboard. Binds a device (and optionally a sensor_type + field) to a visual style, colour, size, and number of points. Stored in `iot_dashboard_widgets`.

**Workflow**  
A directed node graph (stored as JSON) that can be triggered by a new reading or manually. Can produce side effects: append to Google Sheets, queue a command for a device, call an external HTTP endpoint, etc. Powered by the `WorkflowEngine`.

**Ingest**  
The act of a device (or simulator) calling `POST /iot/data` with credentials and a payload. This is the primary way data enters the system. Also evaluates workflows and appends to connected Sheets as side effects.

**Command**  
A message (command name + payload) that the server wants a device to act on. Queued in `device_commands`, delivered the next time the device calls the ingest or poll endpoint, then marked delivered.

**Google Service Account**  
A special Google identity (email address) used by the platform's background worker to append rows to user spreadsheets. Users must explicitly share their sheet with this address.

**last_seen_at**  
Timestamp on the device row, updated on every successful authenticated call from the device (ingest or poll). Used for "online" status, sorting the device list, and workflow conditions such as "has not reported recently".

**sensor_type**  
Optional string label supplied by the device on each reading (e.g. `dht11`, `battery`). Used for filtering, grouping, and labelling series. Not required; many simple devices omit it.

**Payload**  
The free-form JSON object sent in the `data` field of an ingest request and stored verbatim in the `payload` column. The dashboard walks it to discover numeric fields for charting.