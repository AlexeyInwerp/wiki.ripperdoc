---
title: "Apple T2 Platform"
linkTitle: "Apple T2 Platform"
date: 2026-04-19
weight: 20
description: "Overview of Apple's T2 security chip platform, from a repair and data-recovery perspective. Mirrored from repair.wiki."
categories: [macOS, T2, Platform]
tags: [T2, secure boot, SSD, EFI, Intel Mac]
---

{{% alert title="Mirrored article" color="primary" %}}
This article is authored by Alexey Lavrov and originally published on [repair.wiki](https://repair.wiki/w/Apple_T2_platform). It is mirrored here as part of RipperDoc's open repair knowledge base. The canonical version and revision history are on repair.wiki.
{{% /alert %}}

| Field | Value |
|---|---|
| Type | Troubleshooting/Diagnostics |
| Device(s) | [Apple Laptops](https://repair.wiki/index.php?title=Apple_Laptops) |
| Difficulty | ◉◉◉◌ Hard |

## Devices using T2 / T8012 SoC

All Intel MacBooks 2018-2020

Mac Pro 2019

iMac 2020, iMac Pro

Mac Mini 2018

## Theory

Apple introduced T2 or T8012 in 2018 and discontinued it in 2020, fully fused into the M1 CPU. T2 is essentially a second processor used at the low level of the board, similar to SIO/EC on PC laptops; it serves as a board supervisor device. The most important aspect in repair would be its power state control and role in the power sequence. This chip is very close to the iPhone A10 SoC and was likely introduced as a bridge step to the Apple Silicon platform (its OS is even called BridgeOS, which is a huge hint). Understanding the T2 platform is key to understanding the M1+ platform, as well as the basics of Apple hardware design. Understanding T2/iBoot/BridgeOS platform will also help you to effectively read power sequence.

#### Main functions:

- **SMC block:** Battery charging, sensors management, power enables, and Intel S0-S5 state control.
- **SSD Controller:** The T2/M1 MacBook SSD is essentially a sort of RAID array built on custom-produced Toshiba/Hynix SIP (System in Package) SSDs. Each SSD on board (often called NAND, which is technically not correct) contains its RAID configuration block. To read more, refer to the (upcoming) MacBook SSD Repair page.
- **Encryption processor:** T2 has Apple's own security enclave processor (SEP), which is used for encryption, payment verification, authentication, and Touch ID.
- **eSPI controller:** The Intel EFI image is stored on the T2 firmware service partition as a file; it is fed to the PCH via the eSPI interface. For example to clean ME region after Intel SoC/PCH replacement you need to use DFU Revive in Apple Configurator. This will practically rebuild EFI image on BridgeOS partition.
- **Camera, Keyboard/Trackpad, Touchbar, Audio controller:** Being basically a repurposed iPhone CPU, Apple reuses the audio codec, screen controller, and USB interfaces.
- **Power Controller:** T2 is paired with a universal configurable power IC called CALPE. This chip has dozens of integrated buck regulators, LDOs. Unlike old platforms, it does not have direct enable signals but is controlled via the i2c interface. T2 itself also controls the GPU power sequence and gMUX.
- **Debug interfaces.** T2 is also capable of feeding iBoot log as well as PCH/EFI Log into USB. It was used by Quanta with so called Potassium cable which worked as a debug terminal to read boot log of T2 / Intel PCH. Unfortunately since 2018 iBoot leak, Apple encoded all messages into stripped hashed message which cant be decoded. However PCH log is extremely useful since it can pinpoint issue to RAM, GPU or other component depending on stalled block of EFI. It also shows full AHT log (Diagnostics on D button on start), listing all sensors and their values.

#### Important technical details/differencies

- Unlike old SMC, T2 uses 1.8V level for communication protocols. This leaves board with many level shifters which use two voltage inputs: the recipient and target ones. For example, if you had a surge on 3.3v you can expect them to fail. M1 would have 1.25V logic level, and, again, dozens of level shifters all around the board.
- Unlike usual laptops tradition where logical signal is usually always _L (low if active or enabled), logical signals are NOT inverted, which means high level (For example, DFU_STATUS is high 1.8v when DFU). LID signal will also be "LID_OPEN" and however despite being "iphone-like" screen, Touchbar needs LID_OPEN_L to be on.
- Since T2 serves purpose of SMC now, full SMC reset would require DFU revive/restore to completely wipe battery charging log. T2 stores battery serial number and its charging stats in "SoC ROM" SPI Flash. It is also a good way to check history of the device, say if you are not sure if board was swapped or not.
- Machine Serial number is stored on offset 28A000 of SoC ROM. Its presence is necessary for device activation if T2 has activation flag set after restore. Otherwise you will see error "Activation Server Cannot Be Reached". Do not try to change it to remove activation lock or MDM, that won't work and it won't be discussed in this wiki.

## Firmware

T2 Firmware consists of three main parts:

**LLB (low level bootloader):** stripped version of iBoot stored on SPI Flash (for some reason called SoC ROM) on board.

Most important parts of LLB: SMC (Sensors, Power Control, Battery charging), SSD / ANS2 (Apple Nand Storage 2) Firmware

**BridgeOS:** bigger version of modified watchOS. It is stored on SSD and works on higher level, providing interface to MacOS via USB.

**SEP:** Secure Enclave Processor is a separate core inside of T2 which runs on its own completely separated firmware. The only aspect relevant to the repair is possible SEP Firmware corruption which yields error 9 during DFU restore in some cases.

T2 uses same bootrom as checkm8-vulnerable devices. Checkmate can be used to upload patched ramdisk with SSH access and it seems to be used by OnTrack internal data recovery tool. There are also development iBoot builds available which might help with hard case troubleshooting.

## Power Sequence

T2 Platform power sequence is one of the most confusing and difficult parts in MacBook repairs. Being a mixture of iPhone/iPad naming convention and power stages, it is, however, quite simple and much clearer compared to, say, Lenovo's ThinkEngine nightmare.

The power sequence could be possibly divided into 3 main stages:

1. "Finite state" stage, which does not require any activity from T2:
   - Main G3H power state generators, PPBUS, PP3V3_G3H_RTC.
2. T2-Calpe power stage:
   - Main T2 power states, "S2R", "G3S", "SLP" (not relevant to the Intel platform!),
3. Intel Power stage (controlled by PCH/CPU System Agent):
   - S5-S0 power sequence is more or less standard for Intel platform, S5 rails are asserted by T2-Calpe, SLP_S3# and above is from PCH
   - all sys power good (VR_ON) is also issued by T2
   - EFI image is fed to PCH via eSPI interface.

## T2 Power diagram

*Note: The power sequence diagram image (Powersequence T2 beta 0.1) was not available at time of mirroring — the original file at [repair.wiki](https://repair.wiki/w/File:Powersequence_T2_beta1.png) returned a thumbnail error. See the [canonical article](https://repair.wiki/w/Apple_T2_platform) for the latest version.*

## Power Sequence (detailed)

Please note that a sequential list does not mean that having some deeper steps rules out all previous. During diagnostics try to go through the whole sequence. As an example, PMU_CMK32K_SOC is not required until T2 fully boots iBSS firmware from SocROM. However it would prevent its SMC Firmware part to initiate further power sequence.

*Note: The detailed power sequence diagram (Power sequence ver 5) was not available at time of mirroring — the original file at [repair.wiki](https://repair.wiki/w/File:Power_sequence_ver_5.png) returned a thumbnail error. See the [canonical article](https://repair.wiki/w/Apple_T2_platform) for the latest version.*
