---
layout: default
title: Drivers
nav_order: 2
parent: UEFI
---

# Drivers

Depending on the firmware, a different set of drivers may be required. Loading an incompatible driver may lead the system to unbootable state or even cause permanent firmware damage. Some of the known drivers are listed below:


[AudioDxe](https://github.com/acidanthera/OpenCorePkg)* - [HDA audio support driver]({%link docs/UEFI/audiodxe.md %}) in UEFI firmware for most Intel and some other analog audio controllers. Staging driver, refer to [acidanthera/bugtracker#740](https://github.com/acidanthera/bugtracker/issues/740) for known issues in AudioDxe.

[btrfs_x64](https://github.com/acidanthera/OcBinaryData) - Open source BTRFS file system driver, required for booting with [OpenLinuxBoot]({%link docs/UEFI/openlinuxboot.md %}) from a file system which is now quite commonly used with Linux.

[BiosVideo](https://github.com/acidanthera/OpenCorePkg)* - CSM video driver implementing graphics output protocol based on VESA and legacy BIOS interfaces. Used for UEFI firmware with fragile GOP support (e.g. low resolution). Requires `ReconnectGraphicsOnConnect`. Included in OpenDuet out of the box.

[CrScreenshotDxe](https://github.com/acidanthera/OpenCorePkg)* - Screenshot making driver saving images to the root of OpenCore partition (ESP) or any available writeable filesystem upon pressing `F10`. This is a modified version of [CrScreenshotDxe](https://github.com/LongSoft/CrScreenshotDxe) driver by [Nikolaj Schlej](https://github.com/NikolajSchlej).

[ExFatDxe](https://github.com/acidanthera/OcBinaryData) - Proprietary ExFAT file system driver for Bootcamp support commonly found in Apple firmware. For Sandy Bridge and earlier CPUs, the `ExFatDxeLegacy` driver should be used due to the lack of `RDRAND` instruction support.

[ext4_x64](https://github.com/acidanthera/OcBinaryData) - Open source EXT4 file system driver, required for booting with [OpenLinuxBoot]({%link docs/UEFI/openlinuxboot.md %}) from the file system most commonly used with Linux.

[HfsPlus](https://github.com/acidanthera/OcBinaryData) - Recommended. Proprietary HFS file system driver with bless support commonly found in Apple firmware. For Sandy Bridge and earlier CPUs, the `HfsPlusLegacy` driver should be used due to the lack of `RDRAND` instruction support.

[HiiDatabase](https://github.com/acidanthera/audk)* - HII services support driver from `MdeModulePkg`. This driver is included in most types of firmware starting with the Ivy Bridge generation. Some applications with GUI, such as UEFI Shell, may need this driver to work properly.

[EnhancedFatDxe](https://github.com/acidanthera/audk) - FAT filesystem driver from `FatPkg`. This driver is embedded in all UEFI firmware and cannot be used from OpenCore. Several types of firmware have defective FAT support implementation that may lead to corrupted filesystems on write attempts. Embedding this driver within the firmware may be required in case writing to the EFI partition is needed during the boot process.

[NvmExpressDxe](https://github.com/acidanthera/audk)* - NVMe support driver from `MdeModulePkg`. This driver is included in most firmware starting with the Broadwell generation. For Haswell and earlier, embedding it within the firmware may be more favourable in case a NVMe SSD drive is installed.

[OpenCanopy](https://github.com/acidanthera/OpenCorePkg)* - [OpenCore plugin]({%link docs/UEFI/opencanopy.md %}) implementing graphical interface.

[OpenRuntime](https://github.com/acidanthera/OpenCorePkg)* - [OpenCore plugin]({%link docs/UEFI/opencanopy.md %}) implementing `OC_FIRMWARE_RUNTIME` protocol.


[OpenLinuxBoot](https://github.com/acidanthera/OpenCorePkg)* - [OpenCore plugin]({%link docs/UEFI/opencanopy.md %}) implementing `OC_BOOT_ENTRY_PROTOCOL` to allow direct detection and booting of Linux distributiuons from OpenCore, without chainloading via GRUB.

[OpenNtfsDxe](https://github.com/acidanthera/OpenCorePkg)* - New Technologies File System (NTFS) read-only driver. NTFS is the primary file system for Microsoft Windows versions that are based on Windows NT.

[OpenUsbKbDxe](https://github.com/acidanthera/OpenCorePkg)* - USB keyboard driver adding support for `AppleKeyMapAggregator` protocols on top of a custom USB keyboard driver implementation. This is an alternative to builtin `KeySupport`, which may work better or worse depending on the firmware.

[OpenPartitionDxe](https://github.com/acidanthera/OpenCorePkg)* - Partition management driver with Apple Partitioning Scheme support. This driver can be used to support loading older DMG recoveries such as macOS 10.9 using Apple Partitioning Scheme, or for loading other macOS Installers where these were created using the Apple Partitioning Scheme (creating macOS Installers using GPT avoids the need for this). OpenDuet already includes this driver.


[OpenVariableRuntimeDxe](https://github.com/acidanthera/OpenCorePkg)* - [OpenCore plugin]({%link docs/UEFI/opencanopy.md %}) offering emulated NVRAM support. OpenDuet already includes this driver.

[Ps2KeyboardDxe](https://github.com/acidanthera/audk)* - PS/2 keyboard driver from `MdeModulePkg`. `OpenDuetPkg` and some types of firmware may not include this driver, but it is necessary for PS/2 keyboard to work. Note, unlike OpenUsbKbDxe this driver has no `AppleKeyMapAggregator` support and thus requires `KeySupport` to be enabled.

[Ps2MouseDxe](https://github.com/acidanthera/audk)* - PS/2 mouse driver from `MdeModulePkg`. Some very old laptop firmware may not include this driver but it is necessary for the touchpad to work in UEFI graphical interfaces such as `OpenCanopy`.

[OpenHfsPlus](https://github.com/acidanthera/OpenCorePkg)* - HFS file system driver with bless support. This driver is an alternative to a closed source HfsPlus driver commonly found in Apple firmware. While it is feature complete, it is approximately 3 times slower and is yet to undergo a security audit.

[ResetNvramEntry](https://github.com/acidanthera/OpenCorePkg)* - [OpenCore plugin]({%link docs/UEFI/opencanopy.md %}) implementing `OC_BOOT_ENTRY_PROTOCOL` to add a configurable `Reset NVRAM` entry to the boot picker menu.

[ToggleSipEntry](https://github.com/acidanthera/OpenCorePkg)* - [OpenCore plugin]({%link docs/UEFI/opencanopy.md %}) implementing `OC_BOOT_ENTRY_PROTOCOL` to add a configurable `Toggle SIP` entry to the boot picker menu.

[UsbMouseDxe](https://github.com/acidanthera/audk)* - USB mouse driver from `MdeModulePkg`. Some virtual machine firmware such as OVMF may not include this driver but it is necessary for the mouse to work in UEFI graphical interfaces such as OpenCanopy.

[XhciDxe](https://github.com/acidanthera/audk)* - XHCI USB controller support driver from `MdeModulePkg`. This driver is included in most types of firmware starting with the Sandy Bridge generation. For earlier firmware or legacy systems, it may be used to support external USB 3.0 PCI cards.


Driver marked with `*` are bundled with OpenCore. To compile the drivers from UDK (EDK II) the same command used for OpenCore compilation can be taken, but choose a corresponding package:

```bash
git clone https://github.com/acidanthera/audk UDK
cd UDK
source edksetup.sh
make -C BaseTools
build -a X64 -b RELEASE -t XCODE5 -p FatPkg/FatPkg.dsc
build -a X64 -b RELEASE -t XCODE5 -p MdeModulePkg/MdeModulePkg.dsc
```