# What is the SemiBlock IoT Platform?

The IoT Platform is the persistent cloud memory and control plane for all SemiBlock projects that need to talk to the outside world or remember state across power cycles.

## Why it exists

Visual block programming is fantastic for logic on the device, but most educational and hobby projects eventually need three things the device itself cannot provide reliably:

1. **Long-term storage** of sensor readings (SD cards fill up, get lost, or the device is mobile).
2. **Remote visualization** without having to run a local web server or expose ports.
3. **Automation** that continues even when the board is asleep or the student has gone home.

The SemiBlock IoT Platform solves exactly these three problems with a zero-config, student-friendly web experience.

## What you get

- A personal `/iot` console (no extra accounts or credit cards).
- Device credentials that are safe for students to embed in firmware (the secret is only shown once and can be rotated).
- A schema-less data model — send whatever JSON your sensor produces today; the dashboard will offer the fields it sees.
- Charts that just work: the platform samples recent readings and tells the "Add chart" dialog which numeric keys exist.
- Google Sheets export that requires only sharing a sheet with a documented service-account address (no OAuth dance for the student).
- Visual workflows that can turn a temperature spike into an email, a command sent back to the board, or a row in another system.
- A clean REST surface for advanced users who want to script against their own data.

## Relationship to the editors

- **MicroPython editor** — ships with `iotConnect`, `iotPushReading`, and `iotPushValue` blocks (see the [MicroPython documentation](../semiblock-micropython-doc/toc.md) for block details and generated code).
- **Other editors** (Java, JVM, …) — can use the same HTTP API or will grow equivalent blocks.
- **Plain firmware / Python scripts / Node-RED** — just `POST` JSON to `/iot/data` with the three credential fields. The platform does not care where the data comes from.

## What it is *not*

- It is **not** a full MQTT broker or real-time pub/sub system (though commands give a lightweight pull model).
- It is **not** a general-purpose database you can query with SQL from the device.
- It is **not** a replacement for industrial IoT platforms when you need millions of devices or sub-second control loops.

For classroom, club, and rapid-prototyping use cases it is deliberately simple, visual, and integrated with the rest of the SemiBlock experience.