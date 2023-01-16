---
layout: default
title: PlatformNVRAM Properties
parent: PlatformInfo
nav_order: 5
---

# PlatformNVRAM Properties

1. `BID`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Specifies the value of NVRAM variable `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:HW_BID`.

2. `ROM`
    - **Type:** `plist data`, 6 bytes
    - **Failsafe:** Empty (Not installed)
    - **Description:** Specifies the values of NVRAM variables `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:HW_ROM` and `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:ROM`.

3. `MLB`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Specifies the values of NVRAM variables `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:HW_MLB` and `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:MLB`.

4. `FirmwareFeatures`
    - **Type:** `plist data`, 8 bytes
    - **Failsafe:** Empty (Not installed)
    - **Description:** This variable comes in pair with `FirmwareFeaturesMask`. Specifies the values of NVRAM variables:

    - `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:FirmwareFeatures`
    - `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:ExtendedFirmwareFeatures`

5. `FirmwareFeaturesMask`
    - **Type:** `plist data`, 8 bytes
    - **Failsafe:** Empty (Not installed)
    - **Description:** This variable comes in pair with `FirmwareFeatures`. Specifies the values of NVRAM variables:

    - `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:FirmwareFeaturesMask`
    - `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:ExtendedFirmwareFeaturesMask`

6. `SystemSerialNumber`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Specifies the values of NVRAM variables `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:HW_SSN` and `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:SSN`.

7. `SystemUUID`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Specifies the value of NVRAM variable `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:system-id` for boot services only. The value found on Macs is equal to SMBIOS `SystemUUID`.