---
layout: default
title: Memory Properties
parent: PlatformInfo
nav_order: 4
---

# Memory Properties

1. `DataWidth`
    - **Type:** `plist integer`, 16-bit
    - **Failsafe:** `0xFFFF` (unknown)
    - **SMBIOS:** Memory Device (Type 17) - Data Width
    - **Description:** Specifies the data width, in bits, of the memory. A `DataWidth` of `0` and a `TotalWidth` of `8` indicates that the device is being used solely to provide 8 error-correction bits.

2. `Devices`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Specifies the custom memory devices to be added.
    
    To be filled with `plist dictionary` values, describing each memory device. Refer to the [Memory Devices Properties](#memory-device-properties) section below. This should include all memory slots, even if unpopulated.

3. `ErrorCorrection`
    - **Type:** `plist integer`, 8-bit
    - **Failsafe:** 0x03
    - **SMBIOS:** Physical Memory Array (Type 16) - Memory Error Correction
    - **Description:** Specifies the primary hardware error correction or detection method supported by the memory.

    - `0x01` - Other
    - `0x02` - Unknown
    - `0x03` - None
    - `0x04` - Parity
    - `0x05` - Single-bit ECC
    - `0x06` - Multi-bit ECC
    - `0x07` - CRC

4. `FormFactor`
    - **Type:** `plist integer`, 8-bit
    - **Failsafe:** `0x02`
    - **SMBIOS:** Memory Device (Type 17) - Form Factor
    - **Description:** Specifies the form factor of the memory. On Macs, this should typically be DIMM or SODIMM. Commonly used form factors are listed below.
    
    When `CustomMemory` is `false`, this value is automatically set based on Mac product name.
    When `Automatic` is `true`, the original value from the the corresponding Mac model will be set if available. Otherwise, the value from `OcMacInfoLib` will be set. When `Automatic` is `false`, a user-specified value will be set if available. Otherwise, the original value from the firmware will be set. If no value is provided, the failsafe value will be set.

    - `0x01` - Other
    - `0x02` - Unknown
    - `0x09` - DIMM
    - `0x0D` - SODIMM
    - `0x0F` - FB-DIMM

5. `MaxCapacity`
    - **Type:** `plist integer`, 64-bit
    - **Failsafe:** `0`
    - **SMBIOS:** Physical Memory Array (Type 16) - Maximum Capacity
    - **Description:** Specifies the maximum amount of memory, in bytes, supported by the system.

6. `TotalWidth`
    - **Type:** `plist integer`, 16-bit
    - **Failsafe:** `0xFFFF` (unknown)
    - **SMBIOS:** Memory Device (Type 17) - Total Width
    - **Description:** Specifies the total width, in bits, of the memory, including any check or error-correction bits. If there are no error-correction bits, this value should be equal to `DataWidth`.

7. `Type`
    - **Type:** `plist integer`, 8-bit
    - **Failsafe:** `0x02`
    - **SMBIOS:** Memory Device (Type 17) - Memory Type
    - **Description:** Specifies the memory type. Commonly used types are listed below.

    - `0x01` - Other
    - `0x02` - Unknown
    - `0x0F` - SDRAM
    - `0x12` - DDR
    - `0x13` - DDR2
    - `0x14` - DDR2 FB-DIMM
    - `0x18` - DDR3
    - `0x1A` - DDR4
    - `0x1B` - LPDDR
    - `0x1C` - LPDDR2
    - `0x1D` - LPDDR3
    - `0x1E` - LPDDR4

8. `TypeDetail`
    - **Type:** `plist integer`, 16-bit
    - **Failsafe:** `0x4`
    - **SMBIOS:** Memory Device (Type 17) - Type Detail
    - **Description:** Specifies additional memory type information.
    
    - `Bit 0` - Reserved, set to 0
    - `Bit 1` - Other
    - `Bit 2` - Unknown
    - `Bit 7` - Synchronous
    - `Bit 13` - Registered (buffered)
    - `Bit 14` - Unbuffered (unregistered)

## Memory Device Properties

1. `AssetTag`
    - **Type:** `plist string`
    - **Failsafe:** `Unknown`
    - **SMBIOS:** Memory Device (Type 17) - Asset Tag
    - **Description:** Specifies the asset tag of this memory device.

2. `BankLocator`
    - **Type:** `plist string`
    - **Failsafe:** `Unknown`
    - **SMBIOS:** Memory Device (Type 17) - Bank Locator
    - **Description:** Specifies the physically labeled bank where the memory device is located.

3. `DeviceLocator`
    - **Type:** `plist string`
    - **Failsafe:** `Unknown`
    - **SMBIOS:** Memory Device (Type 17) - Device Locator
    - **Description:** Specifies the physically-labeled socket or board position where the memory device is located.

4. `Manufacturer`
    - **Type:** `plist string`
    - **Failsafe:** `Unknown`
    - **SMBIOS:** Memory Device (Type 17) - Manufacturer
    - **Description:** Specifies the manufacturer of this memory device.
    
    For empty slot this must be set to `NO DIMM` for macOS System Profiler to correctly display memory slots on certain Mac models, e.g. `MacPro7,1`. `MacPro7,1` imposes additional requirements on the memory layout:

    - The amount of installed sticks must one of the following: 4, 6, 8, 10, 12. Using any different value will cause an error in the System Profiler.
    - The amount of memory slots must equal to 12. Using any different value will cause an error in the System Profiler.
    - Memory sticks must be installed in dedicated memory slots as explained on the support page. SMBIOS memory devices are mapped to the following slots: `8`, `7`, `10`, `9`, `12`, `11`, `5`, `6`, `3`, `4`, `1`, `2`.

5. `PartNumber`
    - **Type:** `plist string`
    - **Failsafe:** `Unknown`
    - **SMBIOS:** Memory Device (Type 17) - Part Number
    - **Description:** Specifies the part number of this memory device.

6. `SerialNumber`
    - **Type:** `plist string`
    - **Failsafe:** `Unknown`
    - **SMBIOS:** Memory Device (Type 17) - Serial Number
    - **Description:** Specifies the serial number of this memory device.

7. `Size`
    - **Type:** `plist integer`, 32-bit
    - **Failsafe:** `0`
    - **SMBIOS:** Memory Device (Type 17) - Size
    - **Description:** Specifies the size of the memory device, in megabytes. `0` indicates this slot is not populated.

8. `Speed`
    - **Type:** `plist integer`, 16-bit
    - **Failsafe:** 0
    - **SMBIOS:** Memory Device (Type 17) - Speed
    - **Description:** Specifies the maximum capable speed of the device, in megatransfers per second (MT/s). `0` indicates an unknown speed.