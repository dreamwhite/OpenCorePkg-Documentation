---
layout: default
title: Properties
parent: PlatformInfo
nav_order: 1
---

# Properties

1. `Automatic`
    - **Type:** `plist boolean`
    - **Failsafe:** false
    - **Description:** Generate PlatformInfo based on the `Generic` section instead of using values from the `DataHub`, `NVRAM`, and `SMBIOS` sections.
    
    Enabling this option is useful when Generic section is flexible enough:
    - When enabled `SMBIOS`, `DataHub`, and `PlatformNVRAM` data is unused.
    - When disabled `Generic` section is unused.
    
    **Warning:** Setting this option to `false` is strongly discouraged when intending to update platform information. A `false` setting is typically only valid for minor corrections to `SMBIOS` values on legacy Apple hardware. In all other cases, setting `Automatic` to `false` may lead to hard-to-debug errors resulting from inconsistent or invalid settings.

2. `CustomMemory`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Use custom memory configuration defined in the `Memory` section. This completely replaces any existing memory configuration in SMBIOS, and is only active when `UpdateSMBIOS` is set to `true`.

3. `UpdateDataHub`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Update Data Hub fields. These fields are read from the `Generic` or `DataHub` sections depending on the setting of the `Automatic` property.
    
    _Note:_ The implementation of the Data Hub protocol in EFI firmware on virtually all systems, including Apple hardware, means that existing Data Hub entries cannot be overridden. New entries are added to the end of the Data Hub instead, with macOS ignoring old entries. This can be worked around by replacing the Data Hub protocol using the `ProtocolOverrides` section. Refer to the `DataHub` protocol override description for details.

4. `UpdateNVRAM`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Update NVRAM fields related to platform information.

    These fields are read from the `Generic` or `PlatformNVRAM` sections depending on the setting of the `Automatic` property. All the other fields are to be specified with the NVRAM section.

    If `UpdateNVRAM` is set to `false`, the aforementioned variables can be updated with the [`NVRAM`]({%link docs/NVRAM/introduction.md %}) section. If `UpdateNVRAM` is set to `true`, the behaviour is undefined when any of the fields are present in the `NVRAM` section.

5. `UpdateSMBIOS`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Update SMBIOS fields. These fields are read from the `Generic` or `SMBIOS` sections depending on the setting of the `Automatic` property.

6. `UpdateSMBIOSMode`
    - **Type:** `plist string`
    - **Failsafe:** `Create`
    - **Description:** Update SMBIOS fields approach:

    - `TryOverwrite` - `Overwrite` if new size is <= than the page-aligned original and there are no issues with legacy region unlock. `Create` otherwise. Has issues on some types of firmware.
    - `Create` - Replace the tables with newly allocated EfiReservedMemoryType at AllocateMaxAddress without any fallbacks.
    - `Overwrite` - Overwrite existing gEfiSmbiosTableGuid and gEfiSmbiosTable3Guid data if it fits new size. Abort with unspecified state otherwise.
    - `Custom` - Write SMBIOS tables (`gEfiSmbios(3)TableGuid`) to `gOcCustomSmbios(3)TableGuid` to workaround firmware overwriting SMBIOS contents at ExitBootServices. Otherwise equivalent to `Create`. Requires patching AppleSmbios.kext and AppleACPIPlatform.kext to read from another GUID: `"EB9D2D31" - "EB9D2D35"`
    (in ASCII), done automatically by `CustomSMBIOSGuid` quirk.
    
    _Note:_ A side effect of using the `Custom` approach that it makes SMBIOS updates exclusive to macOS, avoiding a collision with existing Windows activation and custom OEM software but potentially obstructing the operation of Apple-specific tools.

7. `UseRawUuidEncoding`
    - **Type:** `plist boolean`
    - **Failsafe:** false
    - **Description:** Use raw encoding for SMBIOS UUIDs.

    Each UUID `AABBCCDD-EEFF-GGHH-IIJJ-KKLLMMNNOOPP` is essentially a hexadecimal 16-byte number. It can be encoded in two ways:

    - `Big Endian` - by writing all the bytes as they are without making any order changes (`{AA BB CC DD EE FF GG HH II JJ KK LL MM NN OO PP}`). This method is also known as [RFC 4122](https://tools.ietf.org/html/rfc4122) encoding or `Raw` encoding.
    - `Little Endian` - by interpreting the bytes as numbers and using Little Endian byte representation (`{DD CC BB AA FF EE HH GG II JJ KK LL MM NN OO PP}`).
    
    The SMBIOS specification did not explicitly specify the encoding format for the UUID up to SMBIOS 2.6, where it stated that `Little Endian` encoding shall be used. This led to the confusion in both firmware implementations and system software as different vendors used different encodings prior to that.

    - Apple uses the `Big Endian` format everywhere but it ignores SMBIOS UUID within macOS.
    - `dmidecode` uses the `Big Endian` format for SMBIOS 2.5.x or lower and the `Little Endian` format for 2.6
    and newer. Acidanthera [dmidecode](https://github.com/acidanthera/dmidecode) prints all three.
    - Windows uses the `Little Endian` format everywhere, but this only affects the visual representation of the
    values.

    OpenCore always sets a recent SMBIOS version (currently 3.2) when generating the modified DMI tables. If `UseRawUuidEncoding` is enabled, the `Big Endian` format is used to store the `SystemUUID` data. Otherwise, the `Little Endian` format is used.
    
    _Note:_ This preference does not affect UUIDs used in DataHub and NVRAM as they are not standardised and are added by Apple. Unlike SMBIOS, they are always stored in the `Big Endian` format.

8. `Generic`
    - **Type:** `plist dictionary`
    - **Description:** Update all fields in `Automatic` mode.
    
    _Note:_ This section is ignored but may not be removed when `Automatic` is `false`.

9. `DataHub`
    - **Type:** `plist dictionary`
    - **Description:** Update Data Hub fields in non-`Automatic` mode.
    
    _Note:_ This section is ignored and may be removed when `Automatic` is `true`.

10. `Memory`
    - **Type:** `plist dictionary`
    - **Description:** Define custom memory configuration.
    
    _Note:_ This section is ignored and may be removed when `CustomMemory` is `false`.

11. `PlatformNVRAM`
    - **Type:** `plist dictionary`
    - **Description:** Update platform NVRAM fields in non-`Automatic` mode.
    
    _Note:_ This section is ignored and may be removed when `Automatic` is `true`.

12. `SMBIOS`
    - **Type:** `plist dictionary`
    - **Description:** Update SMBIOS fields in non-`Automatic` mode.
    
    _Note:_ This section is ignored and may be removed when `Automatic` is `true`.