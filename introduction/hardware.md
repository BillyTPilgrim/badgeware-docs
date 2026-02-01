# Introduction

A huge upgrade from the original Badger — launched four years ago — now better than ever.

Tougher, housed in a sleek shell, and powered by a built-in battery.

On the board we've packed in a bunch of great features:

- RP2350 Dual-core ARM Cortex-M33 @ 200MHz
- 512kB SRAM
- 16MB QSPI XiP flash
- 320x240 full colour IPS display (currently pixel doubled to 160x120)
- 2.4GHz WiFi and Bluetooth 5
- 1000mAh chargeable battery (8 hours runtime)
- Qw/ST and SWD ports for accessories and debugging
- Five front facing buttons
- Battery voltage monitoring and charge status
- USB-C port for charging and programming
- RESET / BOOTSEL buttons
- 4-zone LED backlight
- Durable polycarbonate case with lanyard fixings


# Features

## Processor

Badgeware is powered by the RP2350, a small, but mighty microcontroller sporting dual Cortex M33+ cores. We clock it at 200MHz which provides plenty of oomph for ambitious projects.

You can expect a tasty boost in performance - our "real world" MicroPython tests are running up to 2x faster compared to RP2040, and floating point number crunching in C/C++ is up to 20x faster thanks to the hardware floating point unit. The extra on-chip RAM will make a big difference when performing memory intensive operations (such as working with higher resolution displays) and even more can be added thanks to external PSRAM support (which we have!).

## Flash storage and RAM

Alongside the RP2350 we've added a 16MB flash chip for storing Badgeware's firmware and your code and assets. The filesystem is split into two partitions, one for your code and one for applications to write to at run-time allowing them to store state or cache downloaded data.


, this is paired with an 8MB PSRAM chip ensuring that you have heaps of heap to work with in MicroPython.

## Connectivity

2.4GHz WiFi and Bluetooth 5

## Battery and charger

A 1,000mAh battery powers your badge - it's good for up to 100 days of standby and 12 hours of active use. Recharge it by simply plugging in a USB-C cable!

## Expansion

Qw/ST and SWD ports for accessories and debugging.

You can easily connect our wide range of Qw/ST compatible breakouts to add all sorts of great features to your badge.