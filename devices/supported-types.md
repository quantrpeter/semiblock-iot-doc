# Supported Hardware Types

When you create a device you can optionally pick a type. This is purely informational today — it does not change any behaviour or validation — but it helps students and teachers keep track of what is actually deployed.

## Current options in the UI

- ESP32 (and ESP32-S2/S3/C3 variants)
- ESP8266
- Arduino Uno / Nano / Mega
- Raspberry Pi (any model running Linux + Python or another HTTP client)
- micro:bit (v1 or v2, usually via MicroPython or MakeCode + a gateway)
- "Other" (custom boards, gateways, phones acting as bridges, etc.)

## How the type is used

- Displayed as a small badge or icon in the Devices list and on device detail cards.
- Can be used as a filter in future versions of the Data Explorer or when generating starter code.
- Helps the teacher quickly see "we have 12 ESP32 weather stations and 3 micro:bit soil sensors".

## Adding a new type

Because the list is currently a simple enum / select in the frontend, adding a new official type requires a small code change in the SPA and possibly a migration if it ever becomes a foreign key. For now, just use "Other" and put the real board name in the free-text description field.

## Firmware implications

The type you choose has **no effect** on the ingest protocol. An ESP32 and a Raspberry Pi both call exactly the same `POST /iot/data` with the same three credential fields and the same JSON shape. The blocks in the visual editors may offer slightly different sensor categories based on what that editor targets, but the cloud side is identical.