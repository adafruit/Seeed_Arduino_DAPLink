# Seeed_Arduino_DAPLink
An Arduino Library for Seeed Arduino DAPLink.


## Get Started
1. Download the Adafruit TinyUSB Library from: [Adafruit_TinyUSB_Arduino](https://github.com/adafruit/Adafruit_TinyUSB_Arduino)

2. Arduino IDE tool-->Select the board: Seeeduino samd Serial board.

3. Arduino IDE tool-->Select the USB Stack: TinyUSB.

4. Arduino IDE tool-->Select the COM port.

## How To Use

Most of Adafruit boards featuring M0, M4 and nRF52840 are able to run the daplink sketch. It allows us to flash/burn bootloader of any ARM Cortex-M MCU (M0, M4, nRF52832, nRF52840 etc ..) and step debugging with gdb and/or supported IDE such as Keil, IAR. For your convenience, a precompiled daplink firmware in uf2 format for specific board can be found at [release page](https://github.com/adafruit/Seeed_Arduino_DAPLink/releasesdaplink)

## Hardware setup

Connecting  SWD interface as following pin mapping:

| DAPLink | Target | Note              |
| ------- | ------ | ----------------- |
| A0      | SWIO   | required          |
| A1      | SWCLK  | required          |
| A2      | nReset | optional/required |

Although nReset is optional is in most case, it can be needed in certain situation e.g most bootloader will lock/protect its flash portion before giving control to application. If we want to burn a new bootloader on a board, nReset is needed to reset and halt to prevent bootloader to lock the flash.

### Target SAMD21 & SAMD51

To flash or debug with samd21 & samd51 as target, we can use openOCD. However the current latest of 0.10.0 is released in 2017 and doesnt't support samd51. Therefore we will need to use a recent openocd code tree, luckily Xpacks does provide very-recent pre-compiled binaries for most platform including RPi here https://github.com/xpack-dev-tools/openocd-xpack/releases.


#### Flash

For example, to flash bootloader to samd21

```
$ openocd -f interface/cmsis-dap.cfg -f target/at91samdXX.cfg -c "init; reset; halt; at91samd bootloader 0;program bootloader-itsybitsy_m0-v3.10.0.bin verify reset exit 0"
```

For samd51 (note atsame5x.cfg and atsame5 command only available with recent openocd code tree as mentioned above)

```
$ openocd -f interface/cmsis-dap.cfg -f target/atsame5x.cfg -c "init; reset; halt; atsame5 bootloader 0;program bootloader-itsybitsy_m4-v3.10.0.bin verify reset exit 0"
```

#### GDB Debug

To debug with GDB, you can need to launch openOCD first. for samd21

```
$ openocd -f interface/cmsis-dap.cfg -f target/at91samdXX.cfg -c "init; reset; halt"
```

samd51

```
$ openocd -f interface/cmsis-dap.cfg -f target/atsame5x.cfg -c "init; reset; halt;"
```

Then you could launch GDB and connect to target remote with port 3333 (default). Once connected, you could step through the program for debugging.

```
$ arm-none-eabi-gdb Blink.ino.elf
(gdb) target extended-remote :3333
```

For more information on how to use GDB, check out [GDB tutorial here](https://learn.adafruit.com/debugging-the-samd21-with-gdb)

### Target nRF52832 and nRF52840

In addition to using openOCD to flash and debug nRF52832/nRF52840 as samd with  `target/nrf52.cfg` (for both 832 and 840) e.g

```
$ openocd -f interface/cmsis-dap.cfg -f target/nrf52.cfg
```

We can also use [pyOCD](https://github.com/mbedmicro/pyOCD) which is purposely written by ARM to work with DAPLink on supported targets including nRF52. First you need to install pyocd via pip (for more advanced installation please visit pyOCD repo for more detail)  

```
$ pip install -U pyocd
```

#### Flash

To flash bootloader or application hex file. Note `-t nrf52` is for nRF52832, for nRF52840 it is `-t nrf52840`

```
$ pyocd flash -t nrf52840 feather_nrf52840_express_bootloader-0.3.2_s140_6.1.1.hex
$ pyocd flash -t nrf52 feather_nrf52832_express_bootloader-0.3.2_s132_6.1.1.hex
```

#### GDB Debug

To start GDB server with pyocd

```
$ pyocd gdbserver -t nrf52840
$ pyocd gdbserver -t nrf52
```

Afterward, you could start debugging with arm-none-eabi-gdb which is simillar as samd

```
$ arm-none-eabi-gdb Blink.ino.elf
(gdb) target extended-remote :3333
```

## Contributing
Contributing to this software is warmly welcomed. You can do this basically by<br>
[forking](https://help.github.com/articles/fork-a-repo), committing modifications and then [pulling requests](https://help.github.com/articles/using-pull-requests) (follow the links above<br>
for operating guide). Adding change log and your contact into file header is encouraged.<br>
Thanks for your contribution.

Seeed Studio is an open hardware facilitation company based in Shenzhen, China. <br>
Benefiting from local manufacture power and convenient global logistic system, <br>
we integrate resources to serve new era of innovation. Seeed also works with <br>
global distributors and partners to push open hardware movement.<br>