# Termux System Monitor

[Русская версия](README.ru.md)

A lightweight terminal system monitor for **Android (Termux)** and **Windows**, written in pure Bash and PowerShell. No dependencies, no root required for the basics.

Two tools in one repo:

| Tool | Runs on | What it does |
|------|---------|--------------|
| `termux/sysmon.sh` | Android phone (Termux) | Shows the phone's own battery, CPU, RAM, swap, storage, temperatures and top processes |
| `pc/pc-server.ps1` + `termux/pcmon.sh` | PC (server) and phone (client) | Streams your **PC's** stats to the Termux terminal over USB (`adb reverse`) or Wi-Fi |

## Features

- Colored progress bars (green / yellow / red by load)
- Battery level, charging state, temperature, voltage, health (Android)
- CPU load, frequency, core count, thermal zones
- RAM, swap, disk usage
- Top 8 processes by memory
- Auto refresh, Ctrl+C to exit
- Russian-language UI

## 1. Phone monitor (`sysmon.sh`)

In Termux:

```bash
chmod +x sysmon.sh
./sysmon.sh
```

> **Note:** on newer Android versions some files in `/proc` and `/sys` are not readable by regular apps. Fields that can't be read show `н/д` (n/a) or are skipped.

## 2. PC monitor on the phone (USB or Wi-Fi)

### Phone setup (once)

1. Settings -> About phone -> tap **Build number** 7 times.
2. Settings -> System -> Developer options -> enable **USB debugging**.
3. When connecting to the PC, tap **Allow** on the debugging prompt.

### On the PC (PowerShell, in the project folder)

You need [Android platform-tools (adb)](https://developer.android.com/tools/releases/platform-tools).

```powershell
# the phone must show "device", not "unauthorized"
adb devices

# forward the USB port (repeat after every reconnect)
adb reverse tcp:5050 tcp:5050

# start the server and keep the window open
powershell -ExecutionPolicy Bypass -File .\pc\pc-server.ps1
```

Server parameters: `-Port 5050` and `-Interval 3` (refresh seconds).

### On the phone (Termux)

```bash
chmod +x pcmon.sh
./pcmon.sh
```

### Wi-Fi mode (no USB)

Phone and PC must be on the same network. The server prints your PC's addresses on start (`IP для Wi-Fi: ...`).

```bash
./pcmon.sh 192.168.1.xxx
```

You may need to allow port 5050 in Windows Firewall.

## Security

The server listens on all network interfaces, **without authentication or encryption**. Anyone on your network can connect and see the stats (computer name, running processes, disk usage). Use it on trusted networks only, or prefer USB mode with `adb reverse`.

## Project layout

```
termux/sysmon.sh      phone monitor (Termux)
termux/pcmon.sh       client that displays the PC stream (Termux)
pc/pc-server.ps1      PC stats server (PowerShell)
```

## Requirements

- Android with [Termux](https://termux.dev) (for the phone side)
- Windows with PowerShell 5.1+ (for the PC server)
- `adb` for USB mode

## License

MIT
