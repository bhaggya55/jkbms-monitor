# JK-BMS Web Bluetooth Monitor

A single-page, offline-capable web app that connects to a JK-BMS battery
management system over Web Bluetooth and displays live cell voltages,
pack power, state of charge, cell-voltage delta, device info, and the
BMS's full configuration (settings).

Live site: <https://bhaggya55.github.io/jkbms-monitor>

![JK-BMS Web Bluetooth Monitor screenshot](screenshot.jpeg)

## Features

- **Pack Status** — power, SOC, and cell-voltage-delta gauges.
- **Cell Voltages** — a live bar chart of every active cell, with the
  min/max cells highlighted.
- **Pack Summary** — pack voltage/current/power, temperatures, and balance
  current as text.
- **Device Info** — model, hardware/software version, serial number,
  manufacturing date, uptime, power-on count, and the browser-assigned
  Bluetooth identifier (`device.id`/`device.name` — Web Bluetooth
  deliberately hides the real BLE MAC address from web pages, so this is
  the closest available identifier).
- **Settings** — nearly every field from the BMS's SETTINGS frame:
  voltage protection thresholds (UVP/OVP with recovery), balance settings,
  charge/discharge current limits with OCP delay/recovery, over/under
  temperature protection with recovery, short-circuit protection timing,
  and (on JK02_32S packs) extended fields like device address, precharge
  time, and heating settings.
- **Remote Sync** — optionally POST a combined JSON snapshot of Device
  Info, Settings, and Live Data to a URL of your choice on every refresh.
- Auto-reconnect with exponential backoff if the connection drops.
- Only requests live data if the BMS hasn't already streamed it on its
  own in the last 5 seconds, to avoid duplicate frames.

## Usage

Open [index.html](index.html) in a Chromium-based browser
(Chrome or Edge) that supports the Web Bluetooth API, then click **Connect
to JK-BMS** and select your BMS from the device picker. The Pack Status,
Cell Voltages, Pack Summary, and Remote Sync cards appear once connected
and hide again on disconnect; Device Info and Settings appear as soon as
the BMS reports them.

All dependencies (Bootstrap, Chart.js) are vendored locally under `vendor/`
so the page works fully offline — no CDN or internet connection required.

### Remote Sync (webhook)

Enter a URL in the **Remote Sync** card's textbox to have the app `POST` a
JSON payload — `{ deviceInfo, settings, liveData, timestamp }` — to that
URL every time the live data refreshes. The response (or any request
error) is shown in the status area below the textbox; nothing pops up as
an alert. Leave the field empty (or enter an invalid URL) to disable
sending — no request is made and no JSON is even built in that case. The
URL is saved in the browser's `localStorage` and restored automatically
the next time you load the page.

### Auto-reconnect on page reload

The page can automatically reconnect to a previously-paired device on
load, using `navigator.bluetooth.getDevices()`. This API may require
launching Chrome/Edge with:

```
--enable-features=WebBluetoothNewPermissionsBackend
```

(close all browser windows first). Without it, click **Connect** manually
after each reload.
