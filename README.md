# FC6-II — competition-pruned STM32 fork

This branch (`upstream`) is a competition-only prune of
[`kevin-rao1/FC6-II`](https://github.com/kevin-rao1/FC6-II), itself a fork of
AltOS 1.9.22 by Altus Metrum. It keeps the STM32 firmware tree and the two host
utilities needed to produce Intel HEX / combined images. Modifications are
under the original GNU GPL v2-or-later licence (`COPYING`). This project is not
affiliated with Altus Metrum.

## What was removed

* Android application (`altosdroid`) and all desktop Java UI/library code
  (`altosui`, `altosuilib`, `altoslib`, `libaltos`, `micropeak`, `telegps`).
* Map server, packaging, installer/signing, documentation and AVR/LPC/SAMD/non-F1
  targets.
* All host-side radio, GPS, telemetry and device tools under `ao-tools`; only
  `ao-elftohex`, `ao-makebin` and their shared `lib` remain for the build.
* Radio, GPS, APRS, AES/FEC, packet and telemetry implementation files from
  the shared firmware source tree. Header declarations still referenced by
  `src/kernel/ao.h` are retained so the STM32 sources continue to compile.

## What remains

```
ao-tools/     ELF -> HEX / combined-binary helpers only
src/          STM32F1 kernel, drivers, utils and two F1 targets:
                easymega-v3.0     STM32F1 flight-computer base
                stm32f103-nucleo  minimal F103 build reference
COPYING       GPL v2-or-later
```

This branch is the clean STM32 baseline. The rocket-specific constants, pin
map, F32 reference trajectory and rule-13 posture are on the sibling `main`
branch. The ESP32-C3 Rust flight stack is maintained separately and is not part
of this repository.

## Build

```
./autogen.sh --with-arm-cc=arm-none-eabi-gcc
make -C src
```

`nickle`, `arm-none-eabi-gcc`, libusb-1.0 and libelf headers are required.
A hardware-in-the-loop bench with the flight sensors and servos is still
required; this fork is not flight-qualified by compilation alone.
