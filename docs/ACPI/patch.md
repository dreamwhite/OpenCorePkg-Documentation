---
layout: default
title: Patch
nav_order: 5
parent: ACPI
---

# Patch

1. `Base`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Selects ACPI path base for patch lookup (or immediate replacement) by obtaining the offset to the provided path.

    Only fully-qualified absolute paths are supported (e.g. `\_SB.PCI0.LPCB.HPET`).Currently supported object types are: `Device`, `Field`, `Method`.
    
    _Note_: Use with care, not all OEM tables can be parsed. Use `ACPIe` utility to debug. `ACPIe` compiled with `DEBUG=1 make` command produces helpful ACPI lookup tracing.

2. `BaseSkip`
    - **Type:** plist integer
    - **Failsafe:** 0 (Do not skip any occurrences)
    - **Description:** Number of found `Base` occurrences to skip before finds and replacements are applied.

3. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

4. `Count`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Apply patch to all occurrences found)
    - **Description:** Number of occurrences to patch.

5. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set to `true` to apply this ACPI patch.
    
6. `Find`
    - **Type:** plist data
    - **Failsafe:** Empty
    - **Description:** Data to find. Must be equal to `Replace` in size if set.

    Note: Can be empty, when `Base` is specified, immediate replacement after `Base` lookup happens in this case.

7. `Limit`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Search entire ACPI table)
    - **Description:** Maximum number of bytes to search for.

8. `Mask`
    - **Type:** `plist data`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Data bitwise mask used during find comparison. Allows fuzzy search by ignoring not masked (set to zero) bits. Must be equal to `Replace` in size if set.

9. `OemTableId`
    - **Type:** `plist data`, 8 bytes
    - **Failsafe:** All zero (Match any table OEM ID)
    - **Description:** Match table OEM ID equal to this value.

10. `Replace`
    - **Type:** `plist data`
    - **Failsafe:** Empty
    - **Description:** Replacement data of one or more bytes.

11. `ReplaceMask`
    - **Type:** `plist data`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Data bitwise mask used during replacement. Allows fuzzy replacement by updating masked (set to non-zero) bits. Must be equal to `Replace` in size if set.

12. `Skip`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Do not skip any occurrences)
    - **Description:** Number of found occurrences to skip before replacements are applied.

13. `TableLength`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Match any table size)
    - **Description:** Match table size equal to this value.

14. `TableSignature`
    - **Type:** `plist data`, 4 bytes
    - **Failsafe:** All zero (Match any table signature)
    - **Description:** Match table signature equal to this value.

In most cases, ACPI patches are not useful and are harmful:

- Avoid renaming devices with ACPI patches. This may fail or perform improper renaming of unrelated devices (e.g. `EC` and `EC0`), be unnecessary, or even fail to rename devices in certain tables. For ACPI consistency it is
much safer to rename devices at the I/O Registry level, as done by [WhateverGreen](https://github.com/acidanthera/WhateverGreen).
- Avoid patching `_OSI` to support a higher feature set level whenever possible. While this enables a number of workarounds on APTIO firmware, it typically results in a need for additional patches. These are not usually needed on modern firmware and smaller patches work well on firmware that does. However, laptop vendors often rely on this method to determine the availability of functions such as modern I2C input support, thermal adjustment and custom feature additions.
- Avoid patching embedded controller event `_Qxx` just to enable brightness keys. The conventional process to find these keys typically involves significant modifications to DSDT and SSDT files and in addition, the debug kext is not stable on newer systems. Please use the built-in brightness key discovery in [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys) instead.
- Avoid making ad hoc changes such as renaming `_PRW` or `_DSM` whenever possible.

Some cases where patching is actually useful include:

- Refreshing `HPET` (or another device) method header to avoid compatibility checks by `_OSI` on legacy hardware. `_STA` method with `if ((OSFL () == Zero)) { If (HPTE) ... Return (Zero)` content may be forced to always return `0xF` by replacing `A0 10 93 4F 53 46 4C 00` with `A4 0A 0F A3 A3 A3 A3 A3`.
- To provide a custom method implementation within an SSDT, to inject shutdown fixes on certain computers for instance, the original method can be replaced with a dummy name by patching `_PTS` with `ZPTS` and adding a callback to the original method.

The Tianocore [AcpiAml.h](https://github.com/acidanthera/audk/blob/master/MdePkg/Include/IndustryStandard/AcpiAml.h) source file may help with better understanding ACPI opcodes.

_Note:_ Patches of different `Find` and `Replace` lengths are unsupported as they may corrupt ACPI tables and make the system unstable due to area relocation. If such changes are needed, the utilisation of “proxy” patching or the padding of `NOP` to the remaining area could be considered.