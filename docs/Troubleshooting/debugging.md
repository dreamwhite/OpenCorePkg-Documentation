---
layout: default
title: Debugging
nav_order: 4
parent: Troubleshooting
---

# Debugging

Similar to other projects working with hardware OpenCore supports auditing and debugging. The use of `NOOPT` or `DEBUG` build modes instead of `RELEASE` can produce a lot more debug output. With `NOOPT` source level debugging with GDB or IDA Pro is also available. For GDB check [OpenCore Debug](https://github.com/acidanthera/OpenCorePkg/tree/master/Debug) page. For IDA Pro, version 7.3 or newer is needed, and [Debugging the XNU Kernel with IDA Pro](https://www.hex-rays.com/products/ida/support/tutorials/index.shtml) may also help.

To obtain the log during boot serial port debugging can be used. Serial port debugging is enabled in `Target`, e.g. `0xB` for onscreen with serial. To initialise serial within OpenCore use `Init` configuration option under `Misc->Serial` with other values properly set. For macOS the best choice is CP2102-based UART devices. Connect motherboard `TX` to USB UART `RX`, and motherboard `GND` to USB UART `GND`. Use `screen` utility to get the output, or download GUI software, such as [CoolTerm](https://freeware.the-meiers.org).

_Note:_ On several motherboards (and possibly USB UART dongles) PIN naming may be incorrect. It is very common to have `GND` swapped with `RX`, thus, motherboard “`TX`” must be connected to USB UART `GND`, and motherboard “`GND`” to USB UART `RX`.

Remember to enable `COM` port in firmware settings, and never use USB cables longer than 1 meter to avoid output corruption. To additionally enable XNU kernel serial output `debug=0x8` boot argument is needed.