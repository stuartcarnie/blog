---
title: "Fix 'Sidecar device timed out' error"
date: 2025-10-11T00:00:00Z
tags: ["macos", "sidecar", "devicectl"]
aliases:
  - /fix-sidecar-device-timed-out-error/
---

When Sidecar fails with a "device timed out" error, terminating the SidecarRelay process on your iPad typically 
resolves the issue, which saves you rebooting your iPad.

{{< notice note >}}
This is an advanced technique that requires using a terminal and that Xcode to be installed on your Mac.
{{< /notice >}}


## Prerequisites

You'll need the `devicectl` command, which requires Xcode is installed

## Step 1: Find your device

```sh
devicectl list devices
```

This will output a list of connected devices. Note the device identifier (e.g., `Stuart-Carnies-iPad-809.coredevice.local`).

## Step 2: Find the SidecarRelay process

Replace `<DEVICE_ID>` with your device identifier from Step 1:

```sh
devicectl device info processes --device <DEVICE_ID> | grep 'SidecarRelay'
```

This will show the `SidecarRelay` process. Note the process ID (PID) from the output (e.g., `709`).

## Step 3: Kill the process

Replace `<PID>` with the process ID from Step 2 and `<DEVICE_ID>` with your device identifier:

```sh
devicectl device process terminate --kill -p <PID> --device <DEVICE_ID>
```

After terminating the process, try reconnecting.
