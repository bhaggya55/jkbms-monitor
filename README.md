# JK-BMS Web Bluetooth Monitor

A single-page, offline-capable web app that connects to a JK-BMS battery
management system over Web Bluetooth and displays live cell voltages,
pack power, state of charge, and cell-voltage delta.

## Usage

Open [jkbms-monitor.html](jkbms-monitor.html) in a Chromium-based browser
(Chrome or Edge) that supports the Web Bluetooth API, then click **Connect
to JK-BMS** and select your BMS from the device picker.

All dependencies (Bootstrap, Chart.js) are vendored locally under `vendor/`
so the page works fully offline — no CDN or internet connection required.

### Auto-reconnect on page reload

The page can automatically reconnect to a previously-paired device on
load, using `navigator.bluetooth.getDevices()`. This API may require
launching Chrome/Edge with:

```
--enable-features=WebBluetoothNewPermissionsBackend
```

(close all browser windows first). Without it, click **Connect** manually
after each reload.
