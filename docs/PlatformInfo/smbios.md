---
layout: default
title: SMBIOS Properties
parent: PlatformInfo
nav_order: 6
---

# SMBIOS Properties

1. `BIOSVendor`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** BIOS Information (Type 0) - Vendor
    - **Description:** BIOS Vendor. All rules of `SystemManufacturer` do apply.

2. `BIOSVersion`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** BIOS Information (Type 0) - BIOS Version
    - **Description:** Firmware version. This value gets updated and takes part in update delivery configuration and macOS version compatibility. This value could look like `MM71.88Z.0234.B00.1809171422` in older firmware and is described in [BiosId.h](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/Guid/BiosId.h). In newer firmware, it should look like `236.0.0.0.0` or `220.230.16.0.0` (iBridge: `16.16.2542.0.0,0`). iBridge version is read from `BridgeOSVersion` variable, and is only present on macs with T2.

    ```
    Apple ROM Version
        BIOS ID:      MBP151.88Z.F000.B00.1811142212
        Model:        MBP151
        EFI Version:  220.230.16.0.0
        Built by: root@quinoa
        Date: Wed Nov 14 22:12:53 2018
        Revision: 220.230.16 (B&I)
        ROM Version:  F000_B00
        Build - **Type:** Official Build, RELEASE
        Compiler: Apple LLVM version 10.0.0 (clang-1000.2.42)
        UUID: E5D1475B-29FF-32BA-8552-682622BA42E1
        UUID: 151B0907-10F9-3271-87CD-4BF5DBECACF5
    ```

3. `BIOSReleaseDate`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** BIOS Information (Type 0) - BIOS Release Date
    - **Description:** Firmware release date. Similar to `BIOSVersion`. May look like `12/08/2017`.

4. `SystemManufacturer`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Information (Type 1) - Manufacturer
    - **Description:** OEM manufacturer of the particular board. Use failsafe unless strictly required. Do not override to contain `Apple Inc.` on non-Apple hardware, as this confuses numerous services present in the operating system, such as firmware updates, eficheck, as well as kernel extensions developed in Acidanthera, such as Lilu and its plugins. In addition it will also make some operating systems such as Linux unbootable.

5. `SystemProductName`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Information (Type 1), Product Name
    - **Description:** Preferred Mac model used to mark the device as supported by the operating system. This value must be specified by any configuration for later automatic generation of the related values in this and other SMBIOS tables and related configuration parameters. If `SystemProductName` is not compatible with the target operating system, `-no_compat_check` boot argument may be used as an override.

    _Note:_ If `SystemProductName` is unknown, and related fields are unspecified, default values should be assumed as being set to `MacPro6,1` data. The list of known products can be found in `AppleModels`.

6. `SystemVersion`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Information (Type 1) - Version
    - **Description:** Product iteration version number. May look like `1.1`.

7. `SystemSerialNumber`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Information (Type 1) - Serial Number
    - **Description:** Product serial number in defined format. Known formats are described in [macserial](https://github.com/acidanthera/OpenCorePkg/blob/master/Utilities/macserial/FORMAT.md).

8. `SystemUUID`
    - **Type:** `plist string`, GUID
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Information (Type 1) - UUID
    - **Description:** A UUID is an identifier that is designed to be unique across both time and space. It requires no central registration process.

9. `SystemSKUNumber`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Information (Type 1) - SKU Number
    - **Description:** Mac Board ID (`board-id`). May look like `Mac-7BA5B2D9E42DDD94` or `Mac-F221BEC8` in older models. Sometimes it can be just empty.

10. `SystemFamily`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Information (Type 1) - Family
    - **Description:** Family name. May look like `iMac Pro`.

11. `BoardManufacturer`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** Baseboard (or Module) Information (Type 2) - Manufacturer
    - **Description:** Board manufacturer. All rules of `SystemManufacturer` do apply.

12. `BoardProduct`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** Baseboard (or Module) Information (Type 2) - Product
    - **Description:** Mac Board ID (`board-id`). May look like `Mac-7BA5B2D9E42DDD94` or `Mac-F221BEC8` in older models.

13. `BoardVersion`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** Baseboard (or Module) Information (Type 2) - Version
    - **Description:** Board version number. Varies, may match `SystemProductName` or `SystemProductVersion`.

14. `BoardSerialNumber`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** Baseboard (or Module) Information (Type 2) - Serial Number
    - **Description:** Board serial number in defined format. Known formats are described in [macserial](https://github.com/acidanthera/macserial/blob/master/FORMAT.md).

15. `BoardAssetTag`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** Baseboard (or Module) Information (Type 2) - Asset Tag
    - **Description:** Asset tag number. Varies, may be empty or `Type2 - Board Asset Tag`.

16. `BoardType`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (OEM specified)
    - **SMBIOS:** Baseboard (or Module) Information (Type 2) - Board Type
    - **Description:** Either `0xA` (Motherboard (includes processor, memory, and I/O) or `0xB` (Processor/Memory Module). Refer to Table 15 – Baseboard: Board Type for details.

17. `BoardLocationInChassis`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** Baseboard (or Module) Information (Type 2) - Location in Chassis
    - **Description:** Varies, may be empty or `Part Component`.

18. `ChassisManufacturer`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Enclosure or Chassis (Type 3) - Manufacturer
    - **Description:** Board manufacturer. All rules of `SystemManufacturer` do apply.

19. `ChassisType`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (OEM specified)
    - **SMBIOS:** System Enclosure or Chassis (Type 3) - Type
    - **Description:** Chassis type. Refer to Table 17 - System Enclosure or Chassis Types for details.

20. `ChassisVersion`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Enclosure or Chassis (Type 3) - Version
    - **Description:** Should match `BoardProduct`.

21. `ChassisSerialNumber`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Enclosure or Chassis (Type 3) - Version
    - **Description:** Should match `SystemSerialNumber`.

22. `ChassisAssetTag`
    - **Type:** `plist string`
    - **Failsafe:** Empty (OEM specified)
    - **SMBIOS:** System Enclosure or Chassis (Type 3) - Asset Tag Number
    - **Description:** Chassis type name. Varies, could be empty or `MacBook-Aluminum`.

23. `PlatformFeature`
    - **Type:** `plist integer`, 32-bit
    - **Failsafe:** `0xFFFFFFFF` (OEM specified on Apple hardware, do not provide the table otherwise)
    - **SMBIOS:** `APPLE_SMBIOS_TABLE_TYPE133 - PlatformFeature`
    - **Description:** Platform features bitmask (Missing on older Macs). Refer to [AppleFeatures.h](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/IndustryStandard/AppleFeatures.h) for details.

24. `SmcVersion`
    - **Type:** `plist data`, 16 bytes
    - **Failsafe:** All zero (OEM specified on Apple hardware, do not provide the table otherwise)
    - **SMBIOS:** `APPLE_SMBIOS_TABLE_TYPE134 - Version`
    - **Description:** ASCII string containing SMC version in upper case. Missing on T2 based Macs.

25. `FirmwareFeatures`
    - **Type:** `plist data`, 8 bytes
    - **Failsafe:** `0` (OEM specified on Apple hardware, 0 otherwise)
    - **SMBIOS:** `APPLE_SMBIOS_TABLE_TYPE128 - FirmwareFeatures` and `ExtendedFirmwareFeatures`
    - **Description:** 64-bit firmware features bitmask. Refer to [AppleFeatures.h](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/IndustryStandard/AppleFeatures.h) for details. Lower 32 bits match `FirmwareFeatures`. Upper 64 bits match `ExtendedFirmwareFeatures`.

26. `FirmwareFeaturesMask`
    - **Type:** `plist data`, 8 bytes
    - **Failsafe:** `0` (OEM specified on Apple hardware, 0 otherwise)
    - **SMBIOS:** `APPLE_SMBIOS_TABLE_TYPE128 - FirmwareFeaturesMask` and `ExtendedFirmwareFeaturesMask`
    - **Description:** Supported bits of extended firmware features bitmask. Refer to [AppleFeatures.h](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/IndustryStandard/AppleFeatures.h) for details. Lower 32 bits match `FirmwareFeaturesMask`. Upper 64 bits match `ExtendedFirmwareFeaturesMask`.

27. `ProcessorType`
    - **Type:** `plist integer`, 16-bit
    - **Failsafe:** `0` (Automatic)
    - **SMBIOS:** `APPLE_SMBIOS_TABLE_TYPE131 - ProcessorType`
    - **Description:** Combined of Processor Major and Minor types.
    
    Automatic value generation attempts to provide the most accurate value for the currently installed CPU. When this fails, please raise an [issue](https://github.com/acidanthera/bugtracker/issues) and provide `sysctl machdep.cpu` and `dmidecode` output. For a full list of available values and their limitations (the value will only apply if the CPU core count matches), refer to the Apple SMBIOS definitions header [here](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/IndustryStandard/AppleSmBios.h).