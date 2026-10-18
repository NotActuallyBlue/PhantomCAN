# PhantomCAN

A pocket diagnostic and logging dongle for VAG cars. It plugs into the OBD-II port, shows what it is doing on its own
screen, and connects to your phone or laptop over Bluetooth, Wi-Fi or USB.

**Status: in development.** PhantomCAN is still being tested and has not been released yet. Features and behaviour can change, and bugs are expected.

**Firmware downloads** will appear in this page's [Releases](../../releases) section. There is no release yet. Each release will have the update file (`firmware.bin`), a full image for
first-time USB flashing, and a checksum file.

**What it does**

* **Log and tune with the Simos apps.** A fast Bluetooth link for Simos.app and Simos Tools: live ECU and transmission
  logging, ECU info and flashing.
* **Read and clear trouble codes without a phone.** The built-in Diag mode scans every control unit, lists the faults on the
  screen, shows live data and your VIN, and clears codes behind a confirmation step.
* **Work with generic OBD apps.** ELM327 mode makes the dongle look like a standard ELM327 adapter over Bluetooth LE or Wi-Fi,
  for apps that support those adapters.
* **Capture CAN traffic on a computer.** SavvyCAN mode streams raw CAN frames over Wi-Fi, so it works with the dongle in the car.
* **Sleep when the car is off,** and update its own firmware over Wi-Fi.

**Contents:** [What you need](#what-you-need) · [Getting started](#getting-started) · [Choosing a mode](#choosing-a-mode) ·
[Simos mode](#simos-mode) · [Diag mode](#diag-mode) · [ELM327 mode](#elm327-mode) · [SavvyCAN mode](#savvycan-mode) ·
[Auto sleep](#auto-sleep) · [Updating the firmware](#updating-the-firmware) · [Troubleshooting](#troubleshooting) ·
[Specifications](#specifications) · [How this was built](#how-this-was-built) · [Safety and legal](#safety-and-legal) ·
[Credits and licences](#credits-and-licences)

---

## What you need

* A car with a standard OBD-II port that uses **CAN at 500 kbit/s**. Most cars sold in the US since 2008 do. The dongle was tested
  on a 2017 Volkswagen Golf GTI (Mk7); other models are expected to work but have not been tested yet.
* For Simos mode: **Simos.app** (by Tycho) or **Simos Tools** (by Switchleg1).
* For ELM327 mode: an app that supports **ELM327 over Bluetooth LE** or **ELM327 over Wi-Fi**.
* For Diag mode: nothing. The dongle works on its own.

Not supported: CAN FD and DoIP (found on the newest cars), K-line, and older cars that use the TP 2.0 protocol.

## Getting started

1. **Plug the dongle into the OBD-II port,** usually under the dashboard on the driver's side. It starts as soon as it has power and shows its
   status on the screen.
2. **Turn the ignition on** so the dash lights up. For flashing, use ignition on with the engine off. Some cars need a few seconds after the dash
   lights up before the OBD port wakes.
3. **Pick a mode** (see below). Out of the box the dongle starts in Simos mode.
4. **Connect your app,** or use the buttons and screen in Diag mode.

Do not connect the dongle to a computer over USB while it is plugged into a car. Use Bluetooth or Wi-Fi in the car. USB is for
bench use.

## Choosing a mode

The dongle has two buttons: **BOOT** and **KEY**. To change mode, hold **BOOT**. The screen shows what will happen when you let go, so
release when you see the mode you want.

| Hold BOOT for | The screen offers | What happens |
|---|---|---|
| a quick tap | next page | cycles the status, bus monitor and firmware info pages |
| 2 seconds | the next mode | switches to it and restarts |
| every further 1.5 seconds | the mode after that | keep holding to move through Simos, SavvyCAN, Diag and ELM327 |
| about 6.5 to 8 seconds | **Bench sim** | demo mode, no car needed (see below) |
| about 8 to 9.5 seconds | **Wi-Fi update** | firmware update mode |
| 9.5 seconds or more | **Cancel** | restarts in the same mode |

**Pairing Mode.** After the 3-second start-up animation, Simos, SavvyCAN and ELM327 mode show a **PAIRING MODE** screen with a one-minute
countdown, the Bluetooth name and, where it applies, the Wi-Fi name, password and address. Connect your app while it counts down. It goes away
as soon as something connects, when the minute is up, or when you press any button. In Simos and ELM327 mode Bluetooth stops accepting new
connections when the minute ends (the screen says **LOCKED**); **hold KEY for 2 seconds** to open it, and the Pairing Mode screen, for another minute.
Simos Bluetooth has no password, so its screen shows only the name; the Wi-Fi modes show the Wi-Fi password.
SavvyCAN uses Wi-Fi only, so its screen is a reminder of the network details and nothing locks. Diag mode goes straight to its menu.

**About screen.** Hold **KEY** for 5 seconds (in Diag mode, choose **ABOUT** in the menu) to show a QR code that opens this project's page on
GitHub, along with the product name and firmware build. Any button closes it.

The mode you pick is remembered. Bench sim and Wi-Fi update last for one session only: unplug the dongle and plug it back in to return
to your mode.

**Try it without a car.** Hold BOOT for about 7 seconds and release on **Bench sim**. The dongle then runs your chosen mode against
simulated control units (an engine, a transmission, a gateway and an ABS module with a few made-up trouble codes), so you can try Diag
mode or an app without a car. Nothing is sent on a car's CAN bus.

## Simos mode

The default mode. The dongle appears over Bluetooth as **BLE_TO_ISOTP20**, the name Simos Tools looks for.

1. Open Simos.app or Simos Tools and choose the dongle in its Bluetooth device list.
2. Connect. The screen shows **CONNECTED** with the connection details (link speed and ECU and transmission response times).
3. Use the app as normal: ECU info, logging and flashing.

For flashing, keep the car in ignition on with the engine off, and make sure the car's battery and your phone or laptop are well
charged.

Compatibility notes:

* **Simos.app** works with both the engine control unit (ECU) and the transmission control unit (TCU).
* **Simos Tools** logs the engine control unit (ECU), and with the transmission control unit (TCU) enabled it works in the car. Earlier firmware showed "Failed to create PID frame" when the TCU was on, because the ECU's first reply reached the app before the TCU's. The dongle now sends the TCU's reply first for Simos Tools. After that change it worked in the car, with an occasional single retry on the first connection. If the error keeps coming back, use Simos.app, which has always worked with the TCU on this dongle and car.

## Diag mode

Diag mode turns the dongle into a standalone trouble code tool. You use the two buttons: **BOOT** moves to the next item, and **KEY**
selects (a short press of KEY also goes back).

**Scan codes** asks every control unit in the car for its trouble codes. It takes about twenty seconds and lists the modules that answered,
with their codes.

| What the screen shows | What it means |
|---|---|
| **ACT** | the fault is present right now |
| **THIS** | it failed during the current drive |
| **PEND** | seen once, not yet confirmed |
| **STORED** | a confirmed fault that is saved in the module |
| **MIL** | the warning lamp is requested |
| **HIST** (dimmed) | the module only remembers that it failed at some point, since the last clear or earlier in this drive; it is not counted as a fault |

Some modules list codes in their own format. In the engine and transmission they are standard codes such as P0300. Other Volkswagen
and Audi modules report their own six-digit number (for example 003E0A), which the screen shows exactly as the module sent it. Look it up
with your usual VW tool or app to get the name. Simos Tools and Simos.app have their own code readers.

**Names under the codes.** When the dongle knows what a code means, a short name appears on the line under it (for example `RANDOM MISFIRE`). It knows a
few hundred of the common standard engine and transmission codes, plus a small and growing list of codes from other modules that were checked against a
professional scan tool on a 2017 Golf GTI. A code with no name is shown without one rather than guessed at. Other models can use the same module number
for something different, so treat a name from another car as a hint and check it with your usual VW tool.

**Clear codes** asks you to confirm: **hold KEY for 2 seconds**. The dongle then asks every module to clear, reads each one back, and shows
what is really left. A few things to know:

* Clearing also resets the readiness monitors and erases freeze-frame data, and the car must be in ignition on.
* A code that is still present comes straight back.
* Some modules refuse to clear. The screen then shows a short reason such as `NOT SUPP` (not supported), `NOT NOW` (conditions not met),
  `SECURITY` or `SESSION`.
* **HIST** entries are not faults, and a module may keep them when asked to clear.

**Live data** shows engine speed, vehicle speed, coolant temperature, engine load, throttle, intake air temperature, manifold pressure
and the control module voltage.

**Vehicle info** shows the VIN, the warning lamp state and the readiness monitors.

If the scan finds nothing, the screen says why:

* **BUS SILENT:** no traffic was seen at all, so the car's network is asleep. Turn the ignition on, wait a few seconds and try again.
  Some cars need the engine running before the OBD port wakes.
* **BUS IS ALIVE:** the car is talking but no module answered. Check that the dongle is firmly in the port.

## ELM327 mode

In this mode the dongle behaves like a standard ELM327 adapter, so apps that support those adapters can connect to it.

* **Over Bluetooth LE:** connect to **PhantomCAN** from the app's adapter list.
* **Over Wi-Fi:** join the Wi-Fi network **PhantomCAN** (the password is shown on the dongle's screen), then set the app to connect to
  `192.168.0.10` on port `35000`.

The screen shows the Wi-Fi password, whether a phone is connected, and the last command received.

It supports the common CAN OBD-II protocol (ISO 15765-4, 11-bit, 500 kbit/s) and the usual ELM327 commands. It does not support other
protocols, raw frame mode or monitor mode. Apps differ, so if one does not connect, check that it supports your adapter type (Bluetooth LE
or Wi-Fi ELM327).

## SavvyCAN mode

For capturing and analysing raw CAN traffic with [SavvyCAN](https://www.savvycan.com) on a computer, with the dongle in the car.

1. Switch the dongle to SavvyCAN mode.
2. On your computer, join the Wi-Fi network **PhantomCAN** (the password is shown on the dongle's screen; it is the same network and password in
   ELM327 mode, so your computer or phone can remember it once).
3. In SavvyCAN, add a new device connection of the **network connection (GVRET)** type, with the IP address `192.168.0.10` and port `23`.
4. Connect. The screen shows the connection status and counts the frames captured and sent.

The dongle reads at 500 kbit/s. Frames you send from SavvyCAN go out on the car's bus, so only send frames you understand.

**What you will see on the car.** Many modern cars (VW, Audi and others) put a gateway module between the OBD-II port and the car's internal
networks. Through the port you normally see only diagnostic traffic: the replies to requests you send, plus a few status messages. Everyday
broadcast data such as RPM or wheel speed is not forwarded, so a capture on a plain OBD-II connection can look nearly empty. To see that
data you either request it from a module by its address, or connect to the car's network behind the gateway.

**Try it without a car.** Power the dongle from any USB port or charger, hold BOOT for about 7 seconds with SavvyCAN as your mode and release on
**Bench sim**, and connect as above over Wi-Fi. SavvyCAN then shows three test messages that repeat (IDs `0x100` every 10 ms, `0x200` every
20 ms and `0x300` every 100 ms), and the simulated control units answer requests: for example, send ID `0x7DF` with the data `02 01 00` and the
engine and transmission reply on `0x7E8` and `0x7E9`.

At a desk you can also connect the dongle to the computer over USB and add it in SavvyCAN as a serial **GVRET** device (250000 baud). Do not do
this while the dongle is in a car.

## Auto sleep

The OBD-II port has power all the time, so to avoid draining the car's battery the dongle goes into a low-power sleep after **3 minutes**
with no CAN traffic, no connected phone or app, and no button presses. Driving the car, unlocking it, connecting an app or pressing BOOT wakes it. If something merely stirred the car's network and nothing
connects, the dongle goes back to sleep after about 30 seconds instead of staying awake for the full 3 minutes. It does
not sleep while connected over USB, or in Bench sim or Wi-Fi update modes. In SavvyCAN mode it sleeps only when no computer is connected.

Sleep lowers the dongle's power use but does not turn it off completely. **If you leave the car parked for days or weeks, unplug the
dongle.**

## Updating the firmware

You can update over Wi-Fi without any cable.

1. Hold BOOT for about 8 to 9.5 seconds and release on **Wi-Fi update**.
2. On your phone or computer, join the Wi-Fi network shown on the dongle's screen (**PhantomCAN-** followed by four characters that tell dongles
   apart) and enter the password shown there. The password is new every time.
3. Open `http://192.168.4.1` in a browser, choose the `firmware.bin` file from the newest [release](../../releases), and upload it. Compare its SHA-256 with the `SHA256SUMS` file in the
   release if you want to check the download.
4. The dongle restarts by itself. **Wait about 30 seconds before unplugging it.** It checks the new firmware as it starts, and goes back to
   the previous version automatically if the new one does not start properly.

## Troubleshooting

| Problem | Try this |
|---|---|
| The dongle does not show in the app | If the screen says **LOCKED**, hold **KEY** for 2 seconds to open Bluetooth for another minute. Also make sure no other phone is connected to it, or unplug it and plug it back in, then look for **BLE_TO_ISOTP20** (Simos mode) or **PhantomCAN** (ELM327 mode). |
| Nothing answers, or the screen says BUS SILENT | Turn the ignition on and wait a few seconds. Some cars need the engine running before the OBD port wakes. |
| A code comes straight back after clearing | The fault is still present. Fix the cause, then clear again. |
| A module says NOT SUPP, NOT NOW, SECURITY or SESSION when clearing | It refused the request. Other tools may use a method this dongle does not support. |
| The screen is dark | It switches off after a while. Press BOOT to wake it. |
| The dongle does not respond after an update | Wait 30 seconds. If it is still unresponsive, unplug it and plug it back in; it starts the previous firmware if the new one failed. |
| Simos Tools cannot find or connect to the dongle | Do not rename the dongle in Simos mode. Simos Tools only looks for **BLE_TO_ISOTP20**. If it was renamed in Simos.app, rename it back to **BLE_TO_ISOTP20** there (use Simos.app, then power the dongle off and on). |
| Simos Tools shows "Failed to create PID frame" with the TCU enabled | This happened on earlier firmware and is fixed in the current one. A single retry right after connecting can still happen: try again once. If it keeps coming back, use Simos.app for TCU logging, or turn the TCU off in Simos Tools, and open an issue on this page with the app version. |

## Specifications

| | |
|---|---|
| Connection to the car | OBD-II port, CAN at 500 kbit/s (11-bit) |
| Power | 12 V from the OBD-II port |
| Wireless | Bluetooth LE and a 2.4 GHz Wi-Fi access point (WPA2) |
| USB | USB-C, for bench use |
| Display | 1.9 inch colour screen, 170 x 320 |
| Controls | BOOT and KEY buttons |
| Modes | Simos, Diag, ELM327, SavvyCAN, plus Bench sim and Wi-Fi update |
| Protocols | ISO-TP (ISO 15765-2), UDS (ISO 14229), OBD-II (SAE J1979), GVRET |

## How this was built

PhantomCAN is made by Ghostwerks. I designed it, I test it, and I am the person to ask about it. The firmware was written with heavy AI
assistance (Claude, from Anthropic), working from my direction and checked against a real car. It builds on open-source work, credited below, most of all the
[esp32-isotp-ble-bridge](https://github.com/bri3d/esp32-isotp-ble-bridge) by Brian Ledbetter and Switchleg1.

I say this plainly because I think you should know. I am still learning electronics, and I do not write most of the code by hand. That is
the reason I put so much into testing it:

* **Tested in a car, not just on a bench.** The dongle has been used for weeks in a 2017 Volkswagen Golf GTI, including logging sessions of
  more than half an hour, ignition on and engine running, and sleeping and waking with the car. Problems found there were fixed and checked again.
* **Automated tests run after every change.** About 50 desktop tests (58 at the moment) cover the protocol code, plus bench tests that run each mode over
  Bluetooth and Wi-Fi against a built-in simulated car.
* **Failures leave evidence.** The dongle keeps a log in its own memory, including crash reports, so a fault can be traced instead of guessed at.
  I read that log after every drive and look for lost frames, errors, resets and memory use. Several bugs, some of them my fault and some the
  AI's, were found that way and fixed. One example: logging sessions were occasionally losing a few CAN frames at the start, which the log
  showed as damaged replies. The cause was found and fixed, and the next 37-minute drive lost no frames at all.
* **Bug reports are welcome.** Open an issue on this page; logs from the dongle's memory make them much easier to trace.

What is not proven yet, so you can decide for yourself:

* It has been tested on one car, and the hardware is a prototype. A custom board is planned and will be tested again from the start.
* The Bluetooth connection cannot have a password, and clearing codes does not work on every module (see Diag mode).
* Logging, ECU info, trouble codes, ELM327 mode and SavvyCAN mode are tested in the car. I have not yet flashed an ECU or TCU through the dongle myself, so treat flashing as untested.
* The dongle sleeps when the car is off, and it wakes when the car is unlocked or BOOT is pressed, but I have not yet measured how much current it draws while asleep.
* I have not tested it with every vehicle, app version or phone.

## Safety and legal

* Reading trouble codes and live data is safe. **Clearing codes resets readiness monitors,** and anything that writes to a control unit
  (including flashing) can damage it or void a warranty. Use the dongle on your own vehicle and at your own risk. Keep the battery
  charged and the ignition on, with the engine off, during flashing.
* **Do not connect USB to a computer while the dongle is in the car.**
* The Bluetooth connection cannot have a password (the apps do not support one), so the dongle limits when it accepts connections. In
  Simos and ELM327 mode it takes new Bluetooth connections for **one minute** after it starts and after every button press, then stops
  advertising and the screen shows **LOCKED**. Hold **KEY** for 2 seconds to open it for another minute. While the dongle is plugged into a computer over
  USB (desk use), Bluetooth never locks. An app that is already connected is never cut
  off, and the minute starts again when it disconnects. The Wi-Fi networks use a password, which is shown on the dongle's screen. Unplug the
  dongle when you are not using it.
* Modifying a vehicle's emissions equipment or its emissions-related software can be illegal for road use where you live. This dongle only
  reports what a control unit says; you are responsible for complying with local law.
* Volkswagen, Audi, Golf, GTI, ELM327, Bluetooth and the other names used here belong to their owners. This product is not affiliated with or
  endorsed by them. PhantomCAN and Ghostwerks are names of this product and its maker. Simos.app (Tycho) and Simos Tools (Switchleg1) are separate products by their own developers.

## Credits and licences

PhantomCAN is made by Ghostwerks. The firmware is provided as ready-to-install files on the [Releases](../../releases) page, for use on PhantomCAN dongles.

It is built on open-source work, used under the licences below, and the authors' notices are kept here as those licences require:

* The Bluetooth and ISO-TP bridge it grew from, [esp32-isotp-ble-bridge](https://github.com/bri3d/esp32-isotp-ble-bridge) by Brian Ledbetter (bri3d), and its
  [fork](https://github.com/Switchleg1/esp32-isotp-ble-bridge) by Switchleg1 (MIT licence).
* [isotp-c](https://github.com/lishen2/isotp-c) by Shen Li (MIT licence).
* [Fira Sans](https://github.com/mozilla/Fira) by Mozilla, Telefonica, Carrois Corporate and bBox Type (SIL Open Font License 1.1, notice below), used for all text on the screen.
* [ESP-IDF](https://github.com/espressif/esp-idf) by Espressif Systems and the open-source components it includes, under their own licences
  (mostly Apache 2.0, see [its licence page](https://github.com/espressif/esp-idf/blob/master/LICENSE)).

<details>
<summary>MIT licence notices (esp32-isotp-ble-bridge and isotp-c)</summary>

```
MIT License

Copyright (c) 2021 Brian Ledbetter

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

---

isotp-c, https://github.com/lishen2/isotp-c

MIT License

Copyright (c) 2019 Shen Li & Co-Operators

(The permission and warranty text is the same as above.)
```

</details>

<details>
<summary>SIL Open Font License notice (Fira Sans)</summary>

```
Digitized data copyright 2012-2018: The Mozilla Foundation, Telefonica S.A., Carrois Corporate GbR and bBox Type GmbH.

This Font Software is licensed under the SIL Open Font License, Version 1.1.
This license is copied below, and is also available with a FAQ at:
http://scripts.sil.org/OFL

-----------------------------------------------------------
SIL OPEN FONT LICENSE Version 1.1 - 26 February 2007
-----------------------------------------------------------

PREAMBLE
The goals of the Open Font License (OFL) are to stimulate worldwide
development of collaborative font projects, to support the font creation
efforts of academic and linguistic communities, and to provide a free and
open framework in which fonts may be shared and improved in partnership
with others.

The OFL allows the licensed fonts to be used, studied, modified and
redistributed freely as long as they are not sold by themselves. The
fonts, including any derivative works, can be bundled, embedded, 
redistributed and/or sold with any software provided that any reserved
names are not used by derivative works. The fonts and derivatives,
however, cannot be released under any other type of license. The
requirement for fonts to remain under this license does not apply
to any document created using the fonts or their derivatives.

DEFINITIONS
"Font Software" refers to the set of files released by the Copyright
Holder(s) under this license and clearly marked as such. This may
include source files, build scripts and documentation.

"Reserved Font Name" refers to any names specified as such after the
copyright statement(s).

"Original Version" refers to the collection of Font Software components as
distributed by the Copyright Holder(s).

"Modified Version" refers to any derivative made by adding to, deleting,
or substituting -- in part or in whole -- any of the components of the
Original Version, by changing formats or by porting the Font Software to a
new environment.

"Author" refers to any designer, engineer, programmer, technical
writer or other person who contributed to the Font Software.

PERMISSION & CONDITIONS
Permission is hereby granted, free of charge, to any person obtaining
a copy of the Font Software, to use, study, copy, merge, embed, modify,
redistribute, and sell modified and unmodified copies of the Font
Software, subject to the following conditions:

1) Neither the Font Software nor any of its individual components,
in Original or Modified Versions, may be sold by itself.

2) Original or Modified Versions of the Font Software may be bundled,
redistributed and/or sold with any software, provided that each copy
contains the above copyright notice and this license. These can be
included either as stand-alone text files, human-readable headers or
in the appropriate machine-readable metadata fields within text or
binary files as long as those fields can be easily viewed by the user.

3) No Modified Version of the Font Software may use the Reserved Font
Name(s) unless explicit written permission is granted by the corresponding
Copyright Holder. This restriction only applies to the primary font name as
presented to the users.

4) The name(s) of the Copyright Holder(s) or the Author(s) of the Font
Software shall not be used to promote, endorse or advertise any
Modified Version, except to acknowledge the contribution(s) of the
Copyright Holder(s) and the Author(s) or with their explicit written
permission.

5) The Font Software, modified or unmodified, in part or in whole,
must be distributed entirely under this license, and must not be
distributed under any other license. The requirement for fonts to
remain under this license does not apply to any document created
using the Font Software.

TERMINATION
This license becomes null and void if any of the above conditions are
not met.

DISCLAIMER
THE FONT SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO ANY WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT
OF COPYRIGHT, PATENT, TRADEMARK, OR OTHER RIGHT. IN NO EVENT SHALL THE
COPYRIGHT HOLDER BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
INCLUDING ANY GENERAL, SPECIAL, INDIRECT, INCIDENTAL, OR CONSEQUENTIAL
DAMAGES, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF THE USE OR INABILITY TO USE THE FONT SOFTWARE OR FROM
OTHER DEALINGS IN THE FONT SOFTWARE.
```

</details>
