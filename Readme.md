# BLUEPILL STLINK V2 PROGRAMMER README
Make a Stlink V2 programmer/debugger for STM8/STM32 using a STM32F103 MCU

Sometimes one needs a SWD programmer/debugger for a project but a commercial ‘board’ such as a Discovery, Nucleo or $5 - $20 Chinese clone programmer isn’t available, so it can be handy to build your own or use a cheap spare Blue Pill type board to make one.

## Copyright
Licensed under the MIT Copyright.

Originally Posted by [Mecrisp Stellaris](https://mecrisp-stellaris-folkdoc.sourceforge.io/bluepill-stlink-programmer.html)

## Installing STLINK on a STM32F103 MCU based board
Now referred to as _The Board_

Get the Stlink binary in the /files subfolder

## Mini-arm Board
[mini STM32F103C8T6-board](https://mecrisp-stellaris-folkdoc.sourceforge.io/stm32-boards.html?highlight=green%20pill#shenzhen-lc-mini-stm32f103c8t6-board)

Electrically the same as bluepill but has a JTAG connector.

 * connect JTAG connector from mini-arm to Olimex Tiny-H JTAG dongle
 * plug Olimex Tiny-H USB cable into PC
 * run flash-STLinkV2.J16.S4.bin.sh (**STLinkV2.J16.S4.bin**)

## Other boards with a STM32F103
Such as the Bluepill etc

Flash **STLinkV2.J16.S4.bin** to _The Board_ using your normal flashing method.

## After flashing STLinkV2.J16.S4.bin
 * reset board
 * “dmesg” should show “STMicroelectronics STM32 STLink at <whatever device>”

**WARNING!**

“st-info –probe” will FAIL because the firmware we flashed is obsolete, even tho it shows as a “STMicroelectronics STM32 STLink at <whatever device>” above. This is not a problem as we upgrade to the latest STLink firmware in the next step.

## Upgrading to the latest STlinkV2
There are no binaries available for the latest StlinkV2, to upgrade from the obsolete binary that is available online, a Java based ‘updater’ program made by ST is required.

## Install the STlink Java ‘Updater’
 * Get the following package **stsw-link007** from the /files subfolder
 * It’s Java and has packages for all the Linux distros, just follow the instructions
 * Run “java -jar STLinkUpgrade.jar” for the GUI updater
 * Plug The Board USB connector into your PC and run the Updater, it should fetch the latest firmware and flash it to The Board.
 * The Updater will show that the existing FW version is “V2.21.S4” and what it will be updated to, mine was “V2J45S7”
 * Reset board
 * “dmesg” should show “STMicroelectronics STM32 STLink at <whatever device>”

**Note**

The Updater WRITE PROTECTS the MCU, so the updated binary cannot be read and saved (to avoid all this Java based stuff). If the MCU is set to “unprotect” it just erases the Flash. That’s why there are no binaries floating around the Internet.

 * Run “st-info –probe”

With no MCU connected, just The Board (no external target MCU connected) running stlink-V2J45S7 should produce something like the following output:
```
> st-info --probe

Failed to enter SWD mode
Found 1 stlink programmers
  version:    V2J45S7
  serial:     576b507151542723
  flash:      0 (pagesize: 0)
  sram:       0
  chipid:     0x000
  dev-type:   unknown
```
_The Board_ is now updated and thinks it’s a Stlink programmer/debugger running the latest firmware.

## Connect the target MCU header board to the new Stlink
Green STM32F051 UFQFPN32 header board: [GREEN PILL](https://mecrisp-stellaris-folkdoc.sourceforge.io/stm32-boards.html?highlight=green%20pill#why-not-make-your-own-green-pill)

**Note**

You can use any STM32xxxx MCU here, just change the “Pins” list to suit.

## Wiring
Five wires connect _The Board_ to the target chip. Keep the wires as short as possible as SWD is very fast and long wires just won’t work. The usual short “Dupont Cables” will be fine.
|STM32 Functions  |  Bluepill Target Board | Bluepill Stlink Board  | Wire Colors I used |
|--|--| -- | --  |
| GND | Gnd | Gnd | Black  |
| SWCLK | PA-14 (SWCKL) | PB-13 | White  |
| SWDIO | PA-13 (SWDIO)	| PB-14 <-connect to-> PB-12 | Grey  |
| 3.3V | N/A | 3.3V | Red  |
| N/A | 5v | VIN |  Red |
| NRST | 7 (RST) | PB-0 | Yellow |


## Testing that Stlink can “see” the target MCU is connected
```
> st-info --probe

Found 1 stlink programmers
  version:    V2J45S7
  serial:     576b507151542723
  flash:      131072 (pagesize: 1024)
  sram:       20480
  chipid:     0x410
  dev-type:   STM32F1xx_MD

```
## Success!
Success!! the STlink sees the F0 Device connected !! compare this to the “st-info –probe” above with no MCU connected ?

_The Board_ can now be used to flash target mcus using st-flash or )penOCD on the PC. IT can also be used to connect GBD via OpenOCD for debugging.

## St-util
This is part of the st-link package and can be used to connect a GDB server to the Target
```
% ./st-util

st-util
2020-10-28T14:30:42 INFO common.c: F0xx: 8 KiB SRAM, 64 KiB flash in at least 1 KiB pages.
2020-10-28T14:30:42 INFO gdb-server.c: Listening at *:4242...
```
