---
layout: default
title: Generic Properties
parent: PlatformInfo
nav_order: 2
---

# Generic Properties

1. `SpoofVendor`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Sets SMBIOS vendor fields to `Acidanthera`.

    It can be dangerous to use “Apple” in SMBIOS vendor fields for reasons outlined in the SystemManufacturer description. However, certain firmware may not provide valid values otherwise, which could obstruct the operation of some software.

2. `AdviseFeatures`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Updates `FirmwareFeatures` with supported bits.
    
    Added bits to `FirmwareFeatures`:

    - `FW_FEATURE_SUPPORTS_CSM_LEGACY_MODE` (`0x1`) - Without this bit, it is not possible to reboot to Windows
    installed on a drive with an EFI partition that is not the first partition on the disk.
    - `FW_FEATURE_SUPPORTS_UEFI_WINDOWS_BOOT` (`0x20000000`) - Without this bit, it is not possible to reboot to Windows installed on a drive with an EFI partition that is the first partition on the disk.
    - `FW_FEATURE_SUPPORTS_APFS` (`0x00080000`) - Without this bit, it is not possible to install macOS on an APFS disk.
    - `FW_FEATURE_SUPPORTS_LARGE_BASESYSTEM` (`0x800000000`) - Without this bit, it is not possible to install macOS versions with large BaseSystem images, such as macOS 12.
    
    _Note:_ On most newer firmwares these bits are already set, the option may be necessary when "upgrading" the firmware with new features.

3. `MaxBIOSVersion`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Sets `BIOSVersion` to `9999.999.999.999.999`, recommended for legacy Macs when using `Automatic` PlatformInfo, to avoid BIOS updates in unofficially supported macOS versions.

4. `SystemMemoryStatus`
    - **Type:** `plist string`
    - **Failsafe:** `Auto`
    - **Description:** Indicates whether system memory is upgradable in `PlatformFeature`. This controls the visibility of the Memory tab in “About This Mac”.

    Valid values:
    - `Auto` — use the original `PlatformFeature` value.
    - `Upgradable` — explicitly unset `PT_FEATURE_HAS_SOLDERED_SYSTEM_MEMORY` (`0x2`) in `PlatformFeature`.
    - Soldered — explicitly set `PT_FEATURE_HAS_SOLDERED_SYSTEM_MEMORY` (`0x2`) in `PlatformFeature`.
    
    _Note:_ On certain Mac models, such as the `MacBookPro10,x` and any `MacBookAir`, SPMemoryReporter.spreporter will ignore `PT_FEATURE_HAS_SOLDERED_SYSTEM_MEMORY` and assume that system memory is non-upgradable.

5. `ProcessorType`
    - **Type:** plist integer
    - **Failsafe:** `0` (Automatic)
    - **Description:** Refer to SMBIOS `ProcessorType`.

6. `SystemProductName`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified or not installed)
    - **Description:** Refer to SMBIOS `SystemProductName`.

7. `SystemSerialNumber`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified or not installed)
    - **Description:** Refer to SMBIOS `SystemSerialNumber`.

    Specify special string value `OEM` to extract current value from NVRAM (`SSN` variable) or SMBIOS and use it throughout the sections. This feature can only be used on Mac-compatible firmware.

8. `SystemUUID`
    - **Type:** `plist string`, GUID
    - **Failsafe:** Empty (OEM specified or not installed)
    - **Description:** Refer to SMBIOS `SystemUUID`.

    Specify special string value OEM to extract current value from NVRAM (`system-id` variable) or SMBIOS and use it throughout the sections. Since not every firmware implementation has valid (and unique) values, this feature is not applicable to some setups, and may provide unexpected results. It is highly recommended to specify the UUID explicitly. Refer to `UseRawUuidEncoding` to determine how SMBIOS value is parsed.

9. `MLB`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified or not installed)
    - **Description:** Refer to SMBIOS `BoardSerialNumber`.

    Specify special string value `OEM` to extract current value from NVRAM (`MLB` variable) or SMBIOS and use it throughout the sections. This feature can only be used on Mac-compatible firmware.

10. `ROM`
    - **Type:** `plist multidata`, 6 bytes
    - **Failsafe:** Empty (OEM specified or not installed)
    - **Description:** Refer to `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:ROM`.
    
    Specify special string value `OEM` to extract current value from NVRAM (`ROM` variable) and use it throughout the sections. This feature can only be used on Mac-compatible firmware.