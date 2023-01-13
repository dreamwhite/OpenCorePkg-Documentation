---
layout: default
title: Properties
parent: Misc
has_toc: true
nav_order: 2
---

# Properties

1. `Boot`
    - **Type:** `plist dict`
    - **Description:** Apply the boot configuration described in the [Boot Properties]({%link docs/Misc/boot.md %}) section below.

2. `BlessOverride`
    - **Type:** plist array
    - **Failsafe:** Empty
    - **Description:** Add custom scanning paths through the bless model.

    To be filled with `plist string` entries containing absolute UEFI paths to customised bootloaders such as `\EFI\debian\grubx64.efi` for the Debian bootloader. This allows non-standard boot paths to be automatically discovered by the OpenCore picker. Designwise, they are equivalent to predefined blessed paths, such as `\System\Library\CoreServices\boot.efi` or `\EFI\Microsoft\Boot\bootmgfw.efi`, but unlike predefined bless paths, they have the highest priority.

3. `Debug`
    - **Type:** `plist dict`
    - **Description:** Apply debug configuration described in the [Debug Properties]({%link docs/Misc/debug.md %}) section below.

4. `Entries`
    **Type:** `plist array`
    **Failsafe:** Empty
    **Description:** Add boot entries to OpenCore picker.
    To be filled with `plist dict` values, describing each load entry. Refer to the [Entry Properties]({%link docs/Misc/entry.md %}) section below for details.

5. `Security`
    **Type:** `plist dict`
    **Description:** Apply the security configuration described in the [Security Properties](({%link docs/Misc/security.md %})) section below.

6. `Serial`
    - **Type:** `plist dict`
    - **Description:** Perform serial port initialisation and configure PCD values required by `BaseSerialPortLib16550`

    for serial ports to properly function. Values are listed and described in the [Serial Properties]({%link docs/Misc/serial.md %}) and [Serial Custom Properties]({%link docs/Misc/serial.md %}) section below.

    By enabling `Init`, this section ensures that the serial port is initialised when it is not done by firmware. In order for OpenCore to print logs to the serial port, bit `3` (i.e. serial logging) for `Target` under section `Misc->Debug` must be set.

    When debugging with serial ports, `BaseSerialPortLib16550` only recognises internal ones provided by the motherboard by default. If the option `Override` is enabled, this section will override the PCD values listed in [BaseSerialPortLib16550.inf](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/Library/BaseSerialPortLib16550/BaseSerialPortLib16550.inf) such that external serial ports (e.g. from a PCI card) will also function properly. Specifically, when troubleshooting macOS, in addition to overriding these PCD values, it is also necessary to turn the `CustomPciSerialDevice` kernel quirk on in order for the XNU to use such exterior serial ports.

    Refer to [MdeModulePkg.dec](https://github.com/acidanthera/audk/blob/master/MdeModulePkg/MdeModulePkg.dec) for the explanations of each key.

7. `Tools`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Add tool entries to the OpenCore picker.
    
    To be filled with `plist dict` values, describing each load entry. Refer to the [Entry Properties]({%link docs/Misc/entry.md}) section below for details.

    _Note:_ Certain UEFI tools, such as UEFI Shell, can be very dangerous and **MUST NOT** appear in production configurations, paticularly in vaulted configurations as well as those protected by secure boot, as such tools can be used to bypass the secure boot chain. Refer to the [UEFI]({%link docs/UEFI/tools_and_applications.md %}) section for examples of UEFI tools.