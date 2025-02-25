---
title: Fusion Drive
description: This article describes what fusion drive is, from the perspective of data recovery. 
date: 2025-02-25
---
{{% pageinfo %}}
Fusion Drive was introduced in the 2012 iMac and is a successor of "Core Storage," an Apple proprietary software RAID system.
{{% /pageinfo %}}

## What is Fusion Drive
Technically, Fusion Drive is a JBOD (just a bunch of disks), which means both drives are connected consecutively. A kernel-level defragmentation algorithm does all the magic to keep the most recent data on the SSD LBA area.

## Simply get data during SSD upgrade or migration
If both drives are working fine and have no malfunction flags in SMART, you can simply connect both drives to any Mac with a relatively modern macOS, and it should mount. Please note that the USB Gumstick4 adapter works only with AHCI drives and won't work with 2015-2017 iMac drives, which are NVMe. To connect them, you can search for an NVMe to Apple SSD adapter, which costs around $10 and allows you to connect such SSD to a normal NVMe adapter. There are some adapters on the market smart enough to operate with AHCI, SATA, and NVMe devices, but it is not necessary.
If you are doing a migration to a new SSD, the best way would be to keep the original HDD inside the machine, install macOS on a new NVMe drive, then connect the old SSD part using an adapter and use Migration Assistant.

## Disconnected Fusion Drive data recovery
If you have a disconnected Fusion Drive or one of the devices is malfunctioning, the first thing to do is to image the damaged drive. If you do not have a data recovery suite (like PC3000 or similar), you should use [OpenSuperClone](https://github.com/ISpillMyDrink/OpenSuperClone) software. You need a full byte-to-byte image of both drives to operate comfortably.

The next step would be to use UFS Explorer or any other software that can work with RAID partitions. [UFS Explorer](https://www.ufsexplorer.com/) is hands down the best software for logical data recovery and fully supports APFS, even allowing you to extract data directly from snapshots. 
One of the reasons for Fusion Drive disconnection is a damaged partition descriptor. At the very beginning of each drive, there is a short Fusion Drive header which has a partition ID in it. I will probably add a pattern description later, but basically, such recovery requires you to correct its ID so both partitions would have consecutive numbers (for example, ...5 ...6).


