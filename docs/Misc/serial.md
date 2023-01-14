---
layout: default
title: Serial
parent: Misc
has_toc: true
nav_order: 6
---

# Serial

1. `Custom`
    - **Type:** `plist dict`
    - **Description:** Update serial port properties in `BaseSerialPortLib16550`.
    This section lists the PCD values that are used by the `BaseSerialPortLib16550`. When option `Override` is set to `false`, this dictionary is optional.

2. `Init`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Perform serial port initialisation.

    This option will perform serial port initialisation within OpenCore prior to enabling (any) debug logging.
    Refer to the [Debugging]({%link docs/Troubleshooting/debugging.md %}) section for details.

3. `Override`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Override serial port properties. When this option is set to false, no keys from Custom will be overridden.
    This option will override serial port properties listed in the [Serial Custom Properties](#serial-custom-properties) section below.


# Serial Custom Properties

1. `BaudRate`
    - **Type:** `plist integer`
    - **Failsafe:** `115200`
    - **Description:** Set the baud rate for serial port.

    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialBaudRate` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

2. `ClockRate`
    - **Type:** `plist integer`
    - **Failsafe:** `1843200`
    - **Description:** Set the clock rate for serial port.
    
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialClockRate` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

3. `DetectCable`
    - **Type:** plist boolean
    - **Failsafe:** `false`
    - **Description:** Enable serial port cable detection.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialDetectCable` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

4. `ExtendedTxFifoSize`
    - **Type:** `plist integer`
    - **Failsafe:** `64`
    - **Description:** Set the extended transmit FIFO size for serial port.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialExtendedTxFifoSize` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

5. `FifoControl`
    - **Type:** `plist integer`
    - **Failsafe:** `0x07`
    - **Description:** Configure serial port FIFO Control settings.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialFifoControl` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

6. `LineControl`
    - **Type:** `plist integer`
    - **Failsafe:** `0x07`
    - **Description:** Configure serial port Line Control settings.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialLineControl` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

7. `PciDeviceInfo`
    - **Type:** `plist data`
    - **Failsafe:** `0xFF`
    - **Description:** Set PCI serial device information.
    
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialPciDeviceInfo` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).
    
    Note: The maximum allowed size of this option is 41 bytes. Refer to [acidanthera/bugtracker#1954](https://github.com/acidanthera/bugtracker/issues/1954#issuecomment-1084220743) for more details.
    
    Note 2: This option can be set by running the [FindSerialPort](https://github.com/acidanthera/OpenCorePkg/tree/master/Utilities/FindSerialPort) tool.

8. `RegisterAccessWidth`
    - **Type:** `plist integer`
    - **Failsafe:** `8`
    - **Description:** Set serial port register access width.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialRegisterAccessWidth` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

9. `RegisterBase`
    - **Type:** `plist integer`
    - **Failsafe:** 0x03F8
    - **Description:** Set the base address of serial port registers.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialRegisterBase` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

10. `RegisterStride`
    - **Type:** `plist integer`
    - **Failsafe:** `1`
    - **Description:** Set the serial port register stride in bytes.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialRegisterStride` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

11. `UseHardwareFlowControl`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable serial port hardware flow control.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialUseHardwareFlowControl` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).

12. `UseMmio`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Indicate whether the serial port registers are in MMIO space.
    This option will override the value of `gEfiMdeModulePkgTokenSpaceGuid.PcdSerialUseMmio` defined in [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec).