---
title: Tech stuff
layout: default
---

# 2016 Dome design

## Power

Generator: A single Honda EU10i (220v model) powered the installation from ~6pm (sunset) till ~3am, at which time we presumably ran out of fuel (2L). 

AC power was fed into two set of power supplies: the first was a pair of MeanWell NES-350-5 switch mode PSUs. The output from each was split into two pairs of 5VDC outputs, fused at 20A each. Stranded cables of ~14AWG (>1.5mm^2 cross section) was used from the PSU until just before the panels. This was only used to power the LEDs. Box was unventilated, and caused no overheating issues. The second power supply used a Lenovo Thinkpad 90W AC adapter (this would have been used with the likes of the T510). This is essentially a 20V4.5A switch mode PSU. This powered a TDA7492 based 2 channel amplifier rated at 50W x 2. The PSU also provided input to a Mini-Box OpenUPS, which was configured with a 9Ah SLA, and output at 12V. The output was linked up to a 10A buck converter set at 5V, which in turn powered the MR3040 router, and an Intel Z3735G based tablet, running Windows 10. Each of these had its own Li-Ion battery, providing approximately 3hr of operational life. Use of the UPS went some way toward avoiding having these devices run off power in the 15 hours or so away from AC power each day. Less reboots = less hassles, maybe?

## Control box

TP-Link MR3040v2, running OpenWRT + fcserver (more details on GitHub); Amazon 4-port USB 3.0 hub; 4 x Fadecandy. MR3040 powered from second PSU. Small piece of stripboard + double row headers to attach the Fadecandies. Each candy is linked up to a keystone RJ45 connector (only the signal pins). All Ground pins are tied together, along with ground from each wiring harness. The ethernet port of the MR3040 was also exposed, to provide more connectivity options. 

## Wiring harnesses

Four pieces, each connecting 4 panels (except the last one, which only connects 1). On the controller side: connect up to the high current 5V cables (using a Deans T-connector), RJ45 to control box, and ground pin. On the panel side, 4 x Molex-esque 2x2 connectors, providing +5V, GND, D_1 and D_2 to each panel. Cables used were 12AWG stranded wires for power, and Cat5e stranded for signal. 

## Panels

Each panel consists of 120 WS2812b on mini-PCBs. Physically arranged in a zig-zag, forming an equilateral triangle with 15 LEDs on each side. Electrically, all LEDs were wired in parallel for +5V and GND. A single 1000uF 25V capacitor sat near the input. There was a 560ohm terminating resistor between signal input and the first pixel on each strand.

Fadecandy (at least on stock firmware) is limited to 64 LEDs per strand. Each panel is configured as a 64 + 56 pair, with the bottom 5 rows (except one) of LEDs form strand 1. The top 10 rows form strand 2. Within the panel, mostly 20AWG were used for power, and 24AWG for signal. 16AWG wires were used for power between the connector and where the two strands split.

### Wire length and glitching

The distance between Fadecandy boards and the first pixel was limited to approximately 2.3m (1.2m wiring harness, 1.1m in the panel) to reduce glitching. In testing, a 3m + distance caused significant glitching. Use of the terminating resistors helped. Note that the twisted pairs in Cat5e cables are not used as signal and ground pairs. Not sure if that would have allowed longer cable length, but would have complicated wiring significantly (2x RJ45 per Fadecandy). This configuration is used by RGB-123’s WS2812b kit for Beaglebone though.

To place further distance between control electronics and LEDs, we could have also used longer USB cables, and mounted the FCs inline. USB reliably runs at 2m, and possibly works OK at 3m. For the design we did use, the FCs were housed in the same enclosure as the MR3040, and connected using very short (150mm) cables.

## Audio

Other than sharing a DC PSU with the OpenUPS (and presumably ground with everything), the audio system is largely separate from the rest of the system. Analog input into the amplifier board, and a pair of speaker outputs. We used a pair of cheap 6.5”, 4ohm, 2-way coaxial speakers with built-in crossovers, intended for automotive applications. These were mounted in custom folded horn cabinets (they were available, as it happens). This was sufficiently loud (if not excessive) for the installation, where the sound only needed to be clear in the 5x5m area, above ambient noise.

## Software + Configuration

Almost all code are on github. We used stock Fadecandy server as the OPC server. A range of Processing sketches were used to generate the graphics.

Audio playback was achieved via standard tools. Where we had reactive sketches, these used line-in on the host device, which would either be a microphone, or a redirected copy of line-out. Line-out of devices was plugged into the input of the amplifier.

An attempt at running sketches on a Raspberry Pi 2 was unsuccessful, due to GL issues. We suspect this could be made to work, but performance would be a major concern. Maintaining 30fps on Intel machines was a struggle (Core i5 25xxM and Atom Z3735x). Use of WiFi to connect to the MR3040 was sufficient (though we did use wired ethernet as backup).

