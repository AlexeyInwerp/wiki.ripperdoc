---
title: "MacBook suddenly stopped working after the update to macOS 26.4.1"
linkTitle: "MacBook 26.4.1 — DFU error 4042"
date: 2026-04-18
weight: 10
description: "Your MacBook won't boot after the 26.4.1 update. Why a restore fails with error 4042 and how to recover the machine without losing data."
categories: [macOS, Apple Silicon, Firmware]
tags: [DFU, revive, kernel panic, error 4042, IPSW]
---

## Symptoms / Affected machines

{{< figright src="john-ternus-firmware-failure.webp" alt="John Ternus experiencing firmware failure after transporting his Mac in a backpack — no picture and full fan speed" caption="John Ternus experiencing firmware failure after transporting his Mac in a backpack — no picture and full fan speed." >}}

No matter how exactly update was installed, boot failure happens either right after the update or some time later. 

from power perspective - 20V 0.1-0.3A, full system power with slightly warm CPU. No screen, no chime, trackpad haptic or keyboard activity. 
Affected machines so far: 

MacBook Pro from M2 to M4, but probably might happen to MacBook Air too. 

## What's actually happening

The failure is in the boot chain — the iLLB → iBoot → kernel transition. According to the ~~*speculation*~~ investigation so far, the device stops at iBoot in a non-recoverable panic state. no further evidence has been found in the logs yet, and this will be updated as more time with an affected machine allows. The device does not necessarily fail immediately: sometimes the failure only appears on a full restart — which can happen weeks after the faulty update was installed, for example once the battery has gone completely empty. So a device may still boot normally right after the update is installed and only fail weeks later, or whenever the next full reboot occurs.

## The log — error code 4042

A typical excerpt from the log in Console (Console.app):

```
[17:59:01.7501] Finished DFU Restore Phase: Successful
[17:59:01.7505] Changing state from 'Restoring' to 'Transitioning'
[17:59:01.7505] Creating timer to monitor transition
[17:59:01.7505] Creating a timer for 10 minutes
[17:59:01.8554] DFU mode device disconnected
[17:59:01.8554] Device disconnected during transition
[18:09:01.7592] Timer fired to timeout transitioning device
[18:09:01.7596] Changing state from 'Transitioning' to 'Disappeared'
[18:09:01.7596] Device disappeared during transition
[18:09:01.7597] Device isn't booted but USB is up.
[18:09:01.7655] Restore completed, status: 4042
[18:09:01.7655] Elapsed time (in seconds): 607
[18:09:01.7655] Failure Description:
[18:09:01.7655] Depth:0 Code:4042 Error:Gave up waiting for device to transition from DFU state to DFU state.
[18:09:01.7664] AMPDevicesAgent: Restore error 4042
```

In plain language: LLB payload upload was successful. The device then disconnected and never came back into update mode within the 10-minute window. The process gave up and reported 4042. 

## The safe fix

Important warning up front: do not attempt a full Restore. Restore erases all user data *and* hits the same 4042 because the target firmware is the broken 26.4.1.

{{% alert title="The double revive is no longer needed since macOS 26.5" color="info" %}}
Since the 26.5 update, the double revive is normally no longer required. macOS 26.5 corrects the iLLB (low-level bootloader); the stuck boot was most likely in the iLLB → iBoot transition (or onward to the kernel). The faulty 26.4.1 update could not fix itself — it stayed stuck for the same reason the failure happened in the first place.

The simpler path: do a single Revive until the device boots again — then back up the data — and only then run the OS update normally.
{{% /alert %}}

Revive and Restore are done from the Finder on a second Mac since macOS Sonoma (see [DFU mode on Apple Silicon](/wiki/en/docs/mac-repair/apple-silicon/dfu-mode-apple-silicon/)).

### Double Revive — fallback technique

Since macOS 26.5 this path is normally no longer needed. It stays documented as a technique because it can help in similar cases which might happen:

- If a 4042 error occurs, a Revive with an earlier IPSW can fix it. IPSW files per model: [ipsw.me](https://ipsw.me/product/Mac/) and the [Apple Silicon IPSW database (Mr. Macintosh)](https://mrmacintosh.com/apple-silicon-m1-full-macos-restore-ipsw-firmware-files-database/).
- The double revive is an important technique for edge cases where a Revive with the latest version won't go through.

Steps:

1. Put the device into DFU (see [DFU mode on Apple Silicon](/wiki/en/docs/mac-repair/apple-silicon/dfu-mode-apple-silicon/)).
2. Revive (not Restore!) using an earlier IPSW — one version back, e.g. 26.4.0 or the latest 26.3.x. Revive reinstalls firmware and recoveryOS only; user APFS volumes are untouched.
3. The device now boots normally on the earlier firmware. Wait for the login screen.
4. Revive again — this time with the current IPSW.
5. Data preserved; machine is on current firmware.

{{% alert title="Choosing a specific IPSW" color="info" %}}
To pick an IPSW other than the latest, hold the Option key while clicking Revive (or Restore) and select the file. The IPSW's release date must not be earlier than your Mac's release date — an older image simply won't work and fails with a compatibility error.
{{% /alert %}}

## What NOT to do

- Do not Restore — data loss *and* the same 4042 error.
- Do not have the logic board repaired or replaced. There are numerous reports that even several Apple Authorized Service Providers wrote this off as a "hardware" defect. €800 for a new board plus data loss is, in the vast majority of cases, completely unnecessary.

## If you're not sure — find the right repair shop

If you're not sure, look for a repair shop that knows this issue. The usual quote for such a repair should be roughly the same as what people charge for a system installation on a Windows or Mac machine — technically it is almost the same thing.

## Further reading

- [logi.wiki](https://logi.wiki) — technical articles on MacBook repair
- [repair.wiki](https://repair.wiki) — community documentation on device repair
