# C2K RGB Control

## Install and run

Run `Installer\C2K-RGB-Control-Setup-1.0.2.exe`. Setup installs a desktop and Start
menu shortcut. .NET, Python, OpenRGB and NZXT CAM do not need to be installed.
The Windows x64 runtimes, OpenRGB 1.0rc2 engine and NZXT helper are included.
This local installer is unsigned; it does not impersonate a verified publisher.

The `Portable` folder also runs directly: open `C2kRGBControl.exe`. Keep the
complete folder together, including its DLLs, RGBEngine and NZXT subfolders.

## Features

- Live RGB sliders, brightness, hex colour, native colour picker, presets and off.
- Static colour remains on the controller when the app closes.
- NZXT coolant-temperature pump/fan curves, status snapshots, LCD brightness,
  rotation, and the cooler's own coolant-temperature display.
- Settings save under `%LOCALAPPDATA%\C2kRGBControl`, not inside the installation.
- Automatic sign-in restore, enabled by default in Setup and adjustable on the
  Startup page. This applies saved settings once, retries briefly if USB is not
  ready, and exits. No tray icon, resident service or polling loop remains.
- Complete shutdown on close, including any short-lived NZXT helper.
- A private OpenRGB engine runs only while adjusting lighting or restoring settings.
  Its SDK listens on a temporary loopback port. Windows closes the engine when
  C2K exits, including an unexpected termination. No separate OpenRGB setup is needed.
- One Reset to C2K defaults action on the Startup page, with a preview before
  replacing settings: white 50%, pump 50/65/85/100/100% and fan 30/45/70/100/100%
  at 20/30/40/50/59°C coolant, LCD 80%/0°/coolant display, automatic restore on.
  These are application defaults, not a hardware factory reset. A device error is
  reported as a partial reset; settings on an unavailable device cannot be reset.

Only Apply cooling curve changes cooling; opening a page reads status. Preview
values in an unapplied curve are not a readback of the hardware curve. Watch actual
coolant/CPU temperatures when choosing cooling settings. The 59°C coolant point is
fixed at full speed. Saved settings may reset after sleep/power loss or be changed
by another lighting app. Startup restore runs after sign-in, not before login.

## Hardware support and limits

This release targets the tested Gigabyte X870 AORUS ELITE WIFI7 ICE's IT5711
controller (048D:5711), three ARGB headers with 64 LEDs each, and NZXT Kraken Plus
V2 (1E71:3014). It is not a universal cabinet or cooler controller.

Custom LCD image/GIF uploads are not enabled on Kraken firmware 1.0.0 because of
the known liquidctl upload issue #908. The driver does not expose a separate Kraken
RGB channel. Radiator fans wired to motherboard ARGB use the Lighting page.
CPU/GPU-dependent live screen content and software-driven effects require running
software and are not included. No firmware or calibration flashing is performed.

## Source and build

`Source\src` contains the C# WPF application. `Source\helper` contains the separate
Python/liquidctl frontend and pinned dependency list. `Source\assets` holds the
supplied C2K logo and executable icon. `Source\setup.iss` is the Inno Setup script.
End users need no development tools. Developers rebuilding need .NET 9 SDK,
Python 3.13 + PyInstaller, and Inno Setup 6. See `Source\build.ps1`.

Version 1.0.2 replaces the failed custom HID writer with the OpenRGB engine that
controlled this machine successfully. It initializes the three ARGB zones explicitly
and checks engine state after each colour command. This is not an optical readback:
another RGB application can still override the lights. Slider changes are coalesced
and run off the UI thread. Close waits for the latest colour before stopping the engine.

The C# application and OpenRGB are GPL-2.0-only. The separately executed liquidctl
helper is GPL-3.0-or-later. HIDAPI uses its BSD license. Qt runtime libraries remain
separate DLLs. See Licenses and THIRD-PARTY.md for bundled components and source links.
Upstreams: https://gitlab.com/CalcProgrammer1/OpenRGB and https://github.com/liquidctl/liquidctl.
