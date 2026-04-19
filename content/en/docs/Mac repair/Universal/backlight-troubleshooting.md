---
title: "Backlight Troubleshooting"
linkTitle: "Backlight Troubleshooting"
date: 2026-04-19
weight: 30
description: "How to diagnose MacBook backlight failures — from simple checks to logic-board-level repair. Mirrored from logi.wiki."
categories: [Hardware, Display]
tags: [backlight, display, LCD, troubleshooting]
---

{{% alert title="Mirrored article" color="primary" %}}
This article is authored by Alexey Lavrov and originally published on [logi.wiki](https://logi.wiki/index.php/Backlight_Troubleshooting). It is mirrored here as part of RipperDoc's open repair knowledge base. The canonical version and revision history are on logi.wiki.
{{% /alert %}}

[![Steve Jobs demonstrating corroded PPVOUT_SW_LCDBKLT_FB on his MacBook Air](https://logi.wiki/images/thumb/7/72/steve-jobs.webp/300px-steve-jobs.webp.png)](https://logi.wiki/index.php/File:steve-jobs.webp)

*Steve Jobs demonstrating corroded PPVOUT_SW_LCDBKLT_FB on his MacBook Air*

## First things first

1. **<u>MOST IMPORTANT THING EVER!</u>** <u>Never work, test or turn on 13" devices without screwed in edp-tcon connector. If eDP Cable jumps out of the connector, it will destroy your CPU edp IO since backlight VOUT is 0.2MM away from EDP_AUX_N which goes directly to the CPU. on 15" you can swap edp mux IC, but 13" kills cpu instantly. the best practice is to use tweezers and actively discharge caps on backlight rail before disconnecting.</u> Always discharge Backlight caps before unplugging the Display (wait 20-30 seconds or use 10 ohm resistance to discharge it).
2. Before looking for Backlight problem, check if you really have an image. Dozens of times i've seen people troubleshooting backlight, narrowing it down to "no backlight enable" without having a picture too. It is really important to keep in mind, this is valuable for all macs: **if you do not have image, if display is not recognized - there never will be a backlight.** If there was waterdamage on Display connector near the LCD VOUT pin, it is highly likely that CPU EDP Aux line is dead. On 15/16 inch Macbooks you can simply replace gmux IC which acts as sort of protection for CPU/GPU.
3. Check with known good Display if possible. Rule out Flexgate problem (might happen even on "fixed" models). Use [MacBook LCD Screen Compatibility](https://logi.wiki/index.php/MacBook_LCD_Screen_Compatibility) page to find test screen which should have a backlight.
4. **Do PRAM reset**, on T2 and M1 Macs, do DFU revive/restore to rule out T2 IO problem. It is very rare, but not zero chance. especially if the device is clean and lost its backlight after an update. do not underestimate this, there is nothing dumber than having a backlight after 2 hours of soldering and then finally doing PRAM reset "just in case".

## Backlight Circuit ICs used in MacBooks

The backlight circuits in MacBooks manufactured from 2009 to 2020 operate on the same basic principle. Three controllers were used during this period: LP8550, LP8545, and a two-chip circuit consisting of LP8548 and LP8549.

1. LP8550 MacBook Pro PRE 2012 Retina / MacBook Air till 2017. [Datasheet](https://www.ti.com/lit/ds/symlink/lp8550.pdf?ts=1641743667771&ref_url=https%3A%2F%2Fwww.google.com%2F)
2. LP8545 was used on first Retina Macbook 2012 (820-3332, 820-3662). This chip is almost identical to LP8550 [Datasheet](https://www.ti.com/lit/ds/symlink/lp8545.pdf) is also publically available. BOOST SW FET is moved outside the IC, Feedback voltage divider too.
3. 2-chip (LP8548 + LP8549) circuit was first introduced in year 2013 on Retina MacBooks A1502, A1398. MacBook Air uses this chip combination since 2018. There are a few later revisions, but they all seem to be compatible between same gen of the device. for example, all 2012-2015 would work fine. all TBT will work up to M1 models. There is no datasheet available, but basically this is the same LP8545 split into two parts: Boost part + Current Sink/Logical part. There is also driver for Keyboard backlight integrated into LP8548 part.

#### LP8550 and LP8545 Basic operation principle

The LP8550/8545 backlight driver can be divided into three parts:

##### Boost converter.

This section of the IC generates high voltage from PPBUS_G3H and boosts it to the default maximum for the platform when it receives the Enable signal. If the brightness is adjusted to a lower level, it generates a lower voltage to optimize power consumption. The main function of this circuit is to drive LED stripes within the screen, typically through the VOUT or VLED test points on the TCON board.

[![Booster circuit diagram](https://logi.wiki/images/thumb/a/a6/Booster_circuit.jpg/415px-Booster_circuit.jpg)](https://logi.wiki/index.php/File:Booster_circuit.jpg)

Simplified diagram from The LP8545 datasheet. T1 FET and feedback voltage divider are integrated in LP8550.

##### LED-Return / Feedback / Current sink part.

[![LED Return diagram 1](https://logi.wiki/images/f/fa/LED_Return.jpg)](https://logi.wiki/index.php/File:LED_Return.jpg) >> [![LED Return diagram 2](https://logi.wiki/images/thumb/c/ca/LED_Return_2.jpg/374px-LED_Return_2.jpg)](https://logi.wiki/index.php/File:LED_Return_2.jpg)

LED return/feedback/current sink: This part of the IC connects the cathode part of the LED stripe to ground through PWM-controlled FETs (current sinks), which adjusts the brightness of the screen. LED_RETURN_1..6 lines are present on the eDP/LVDS connector, and a broken line can cause uneven lighting on some parts of the screen (so-called "Stagelight" effect).

- Logical part

[![LP8545 Logic part diagram](https://logi.wiki/images/thumb/d/d9/LP8545_Logic_part.jpg/300px-LP8545_Logic_part.jpg)](https://logi.wiki/index.php/File:LP8545_Logic_part.jpg)

This circuit controls PWM, high voltage generation, I²C communication with PCH/MCU/gMUX/tCon processors, and has an EPROM with a stored brightness profile and slopes.

##### Configuring Backlight Slope on fresh LP8550 after replacement

Please refer to [Piernov's LP8550 Slope configuration software and instructional video.](https://logi.wiki/index.php/LP8550_Slope_Configuration) If you use new LP8550 it is quite easy to flash it with original smooth brightness change slopes.

#### LP8548 + LP8549 Combination

The LP8548+LP8549 combination is a split variant of the LP8545. The LP8548 is a BOOST IC with an I²C communication part, controlled by the LP8549 placed on the TCON board since the MacBook Pro 2014. The LP8549's main I²C interface is directly connected to the TCON LCD controller, freeing six pins on the eDP connector. Refer to the 820-3787 schematics/boardview for both chips' locations, as this board has both chips nearby and still has LED_RETURN lines on the eDP connector.

[![820-3787 board showing communication line between LP8548 and LP8549](https://logi.wiki/images/thumb/3/39/820-3787.jpg/300px-820-3787.jpg)](https://logi.wiki/index.php/File:820-3787.jpg)

*As you may see there is a communication line between two chips. LP8549 could be considered as "primary".*

#### LUXE (RAA209100B)

M1 + 2021 and later 14 and 16 inch models use quite different backlight system. It is BGA like LP8550 and there are SPMI lines to the SoC.

As prior models it only outputs backlight if image is present. LED is now a matrix on all backlight Lid.

One of common symptoms on damaged LCD panel would be very blurry monochrome image produced by backlight behind the reset / pixelated LCD.

Instead of PRAM reset, first thing to check would be questioning if the device has original Display or not. If someone installed non original screen on A2442 / A2779 model without chip transfer, backlight will be off untill LID Open / Close trigger loop.

Chip itself is a simple buck-boost converter, sending power to backlight driver inside of the LCD. Secondary backlight driver is located in separate tcon board inside of the screen. It has i2c and SPI lines attached to SoC, however it is most likely interfaced by Parade DP855A LCD MCU inside of the LCD TCON.

Sometimes after fixing waterdamaged LUXE driver, there will be dead power regulator in Backlight tcon. Chip has only apple part number and most likely is some customized slim TI power driver. chip is located closer to the Camera/ALs cable and has PN A2485 is 338S00820. It is only donor-sourced chip ( A2442, A2485 and most likely A2779).

A2442 338S00821

This backlight system also has flickering issue if LCD is transferred without SN cloning (either by editing ROM contents or by transferring Parade + SPI from old LCD)

## Repair and Troubleshooting

### LP8550 / LP8545

**0. Before attempting any repairs, perform a PRAM reset and check the SMC_LID line.**

1. Check Fuse, check if line is shorted / by any damage replace i-pex eDp connector (Refer to [MacBook Parts List](https://logi.wiki/index.php/MacBook_Parts_List)) ALWAYS check for short on cable side, both connected and disconnected. If the pin is burned in many cases it will burn the insulation under the VOUT pin shorting it to ground once inserted. Normal resistance is kOhms.
2. Check if Enable is present on both Backlight power switch and IC itself.
3. Check if FB(Feedback) line is corroded on VIA near the Booster Diode. This is a common issue on MacBook Air boards. To check it, remove D7701 and measure in diode mode (Red probe to ground, black on pin 2 of the D7701). OL would indicate a broken connection to the LP8550
4. Check if there is communication with the backlight IC, and if it has VDDIO and VIN power supply.
5. If nothing helps, replace LP8550 and before putting new one use diode mode to check if all traces are good (0.4-0.8V voltage drops on all relevant pins)

Basically that's it. LP8550 troubleshooting is pretty straightforward compared to later models with dual chip system.

### LP8548 + LP8549

**0. Do a PRAM Reset, check LID, check [Flexgate](https://logi.wiki/index.php/A1706/A1708_TConn_Backlight_Pinout_(FlexGate)) (2016+ Models), replace eDP Cable line before touching this circuit at all.**

Boost circuit part troubleshooting is quite similar (same feedback function, same PPBUS voltage if no boost, for example). However, it is actually being controlled by TCON board which makes troubleshooting a little bit confusing.

If FUSE / boost circuit checks out, check these two testpoints on TCON Board (similar on all devices, new displays have no marks on them)

[![TCON Backlight SDA/SCL testpoints](https://logi.wiki/images/thumb/a/a0/TCON_Backlight_communication.png/300px-TCON_Backlight_communication.png)](https://logi.wiki/index.php/File:TCON_Backlight_communication.png)

*TCON Backlight SDA / SCL testpoints*

If there is communication going on and lines are not shorted (0.4-0.5V drop in diode mode) check VLED voltage under the backlight cable:

[![TCON Backlight VLED Voltage testpoint](https://logi.wiki/images/thumb/3/3b/TCON_Backlight_VOUT.png/300px-TCON_Backlight_VOUT.png)](https://logi.wiki/index.php/File:TCON_Backlight_VOUT.png)

*TCON Backlight VLED Voltage testpoint*

Please note that no backlight AND good voltage on VLED does not necessarily indicate flexgate or dead LED stripe. Since Current Sink side is on TCON Board, it needs to reach LP8548 on Logicboard before it runs PWM driver. (I2C_BKLT_SDA/SCL). It also needs communication with PCH/GMUX (I2C_TCON_SDA/SCL) or EDP_BKLT_PWM+SMBUS_SMC_0_S0_SDA/SCL on older boards.

If you ever messed with 2017-2019 Display firmware you might have seen that ALS / Truetone / Backlight slope is a part of display firmware. Basically

If communication between TCON and LP8548 is disturbed, you might have 50V on VLED and no backlight or 0v VLED but enable signal from GMUX.

So basically, if you do not have high voltage on VLED, troubleshoot the logicboard (enable signal, VDDD / VDDA, Boost circuit, fuse, etc.) but also keep in mind that communication line is also necessary to turn on boost circuit, so the problem might be on TCON/Display side, so check PP5V_S0SW_LCD (display will have picture without 5V power but no backlight and no boost).

If you have high voltage but no backlight, inspect communication lines first. This IC has two separate 5V supplies for booster and communication circuits, so check LP8548 VDDD/VDDA precisely: if it is corroded and supplies for example, only 4.3V to VDDD (oddly it is from the opposite side of datalines), BOOST will work, but SPI communication part of the IC will fail to properly talk back to the TCON board. (also, communication level is 5V unlike 1.8/3.3v on most lines)

Most common symptom of bad TCON side/TCON power /TCON communication would be a short burst on backlight power, you will see it as some voltage on VOUT which will quickly go down as caps discharge.

#### GAY-GAV Backlight repair

If you have classic GAY-GAV PP5V_G3S short circuit and there is a hole in LP8548, after you replace both 5V voltage regulators (PVCCIOS0 U8110 and PVCCEDRAM U7710) and Backlight driver, check Feedback voltage divider (LCDBKLT) and PP5V_S0SW_LCD power switch.

As you may guess, LP8550 may mainfest same symptoms as any of these two chips, so if you stuck with high voltage but no backlight - replace it before opening screen and checking if cable is bad.

### Known test possibilities:

all screens from the same year will turn on backlight. (For example 2019 16" will turn on backlight on 13 and 15 inch screens or even provide sign of image)

If for some reason backlight works during the Apple logo (on boot) and backlight is turned off on user login window or after boot completion, check LAS (Lid Angle Sensor) for liquid (Macbook 16" only).

### T2-M1 Backlight Driver compatibility

T2 = LP8549B1-04 , M1 = LP8549B1 A07

With T2 donor = PPVOUT_BKLT is boosting to 50Volt but no backlight on LCD. No flexgate issue etc. You must have donor from M1.

(credits to iBoff Matt)

## Old troubleshooting guide

### 820-00138 BKLT_EN high but LCDBKLT_EN_L is not being pulled down

this is a pretty tricky one, i found 3-4 deadend posts on badcaps / Rossmann's with no solution for this issue.

The problem is that there is no real 820-00138 boardview available, in most cases there is 820-3662 boardview or similar. however PDF seem to be the right one. The difference between commonly distributed boardview and real one is that there is no U7750 on board, and this is the source for BKLT_SCL + BKLT_SDA. To inspect backlight circuit use 820-00163 or 00462 (this is DG board version) which is totally fine and has absolutely the same backlight circuit design.

These two are critical for backlight to turn on, this is somehow similar to iMac design, where display is the one who really turns backlight and only once image output is stabilized. This might seem overenginered, but it is kind of bugfix for possible graphical artifacts on system poweron.

On 820-00138 I2C_BKLT_SCL + I2C_BKLT_SDA are coming from eDP connector J8300, pins 3 and 4, they measure 5v unlike most other I2C lines (3.3v).

**For 820-4924 with LCDBKLT_EN_L is not being pulled down**

If the connection between U7700 and R7700 is broken on the ISNS rails LCDBKLT_EN_L will stay high. Make sure that there is continuity between U7700 Pin9 to R7700 Pin4 and U7700 Pin10 to R7700 Pin3. The trace goes between the board layers and might look perfectly fine.

### Rossmanns Guide

([https://docs.google.com/presentation/d/1PkeO_lC5WTPScSV3ZzEEjVuDWeQtL2eHK6jEcf7axA0/edit#slide=id.gb813305ed3_143_1678](https://docs.google.com/presentation/d/1PkeO_lC5WTPScSV3ZzEEjVuDWeQtL2eHK6jEcf7axA0/edit#slide=id.gb813305ed3_143_1678)) Pages 145-149 or search for "Standard backlight circuit faults!" if pages change over time

> PRAM RESET! Low boost can be the brightness being down.
>
> Check voltage after backlight diode.
>
> If backlight is 45V or more there is no load on the backlight circuit. Use a known good screen and check the connector.
>
> If backlight is 25V-40V the boost circuit is working. Check the connector, cable, and LCD.
>
> If backlight is PPBus voltage your boost circuit isn't working. Check the feedback trace, LCD driver, and enables.
>
> If backlight is 0 check for short then work your way back toward PPBus. The LCDBKLT_FET, fuse, and enable transistors are all suspect at this point. Also check the voltage dividers.
>
> checking SMC_LID (it shouldn't give an image either if it's missing)
>
> To have boost on this circuit, the LCD must communicate. Be sure to use a known good LCD when troubleshooting.
>
> Measuring before the inductor you should have PPBus, and after it you have some random rounded-square-ish (or triangle-ish) signal that a cheap multimeter won't know what to do with. Measure after the diode unless you are using an oscilloscope.
>
> Missing LCD_BKLT_PWM: Inject voltage or jumper from PP3V3_S0 or similar.
>
> Some great info from Louis Rossmann (User name - zzz) ifixit forum
>
> Most "common" problem with no backlight is ball A5 on LP8550 or LCD connector, but guessing is BS and should not be done here.
>
> put multimeter in diode mode, red probe on ground, black probe on pins 3/4 of LCD connector.
>
> 0.0 or 0.007 means direct short to ground, usually inside LCD connector or LCD cable.
>
> 0.200-0.300 is bad LED driver.
>
> 0.459-0.511 is bad feedback via or corroded away feedback ball under LP8550
>
> 0.565 is corroded solder ball or bad switch trace inside LP8550
>
> Measure voltage along each point in the circuit. Before F9700, after F9700. At output.
>
> At backlight output, measured on pin 3 or 4 of the LCD connector,
>
> 0v means short to ground, blown fuse, or no LCD connected. Check that you see an image on the LCD, check backlight fuse, check board via from FET after fuse to boost coil.
>
> 8v means no short to ground, good fuse, but no boosting. Check BKL_EN is 2.7 to 3v at voltage divider going to enable pin of WLED driver, check BKL_PWM signal exists.
>
> 27v or higher means all good, but your LCD cable or LCD backlight itself is blown.
>
> If you see power along the line, where is it? Where does it stop?
>
> If you replace a fuse with a short to ground anywhere in the line you have wasted your time, and a precious little 0603 package. :(
>
> Lastly if it worked for a few months and then died most likely your problem is on bad feedback via/ball on LP8550, pins 3/4 coming corroded on LCD connector, or BKL_EN voltage divider resistors in that order.
>
> Have fun! Louis Rossmann [^1]

[^1]: repair.wiki
