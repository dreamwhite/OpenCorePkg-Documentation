---
layout: default
title: Mandatory Variables
parent: NVRAM
nav_order: 3
---

# Mandatory Variables

**Warning:** These variables may be added by the [PlatformNVRAM]({%link docs/PlatformInfo/platformnvram.md %}) or [Generic]({%link docs/PlatformInfo/generic.md %}) subsections of the [PlatformInfo]({%link docs/PlatformInfo/introduction.md %}) section. Using `PlatformInfo` is the recommended way of setting these variables.

The following variables are mandatory for macOS functioning:
- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:FirmwareFeatures`
    32-bit `FirmwareFeatures`. Present on all Macs to avoid extra parsing of SMBIOS tables.
- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:FirmwareFeaturesMask`
    32-bit `FirmwareFeaturesMask`. Present on all Macs to avoid extra parsing of SMBIOS tables.
- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:MLB`
    `BoardSerialNumber`. Present on newer Macs (2013+ at least) to avoid extra parsing of SMBIOS tables, especially in `boot.efi`.
- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:ROM`
    Primary network adapter MAC address or replacement value. Present on newer Macs (2013+ at least) to avoid accessing special memory region, especially in `boot.efi`.
