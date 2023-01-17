---
layout: default
title: Emulate
parent: Kernel
nav_order: 5
---

# Emulate

1. `Cpuid1Data`
    - **Type:** `plist data`, 16 bytes
    - **Failsafe:** All zero
    - Description: Sequence of `EAX`, `EBX`, `ECX`, `EDX` values to replace `CPUID (1)` call in XNU kernel.

    This property primarily meets three requirements:
        - Enabling support for an unsupported CPU model (e.g. Intel Pentium).
        - Enabling support for a CPU model not yet supported by a specific version of macOS (typically old versions).
        - Enabling XCPM support for an unsupported CPU variant.
    
    _Note 1:_ It may also be the case that the CPU model is supported but there is no power management supported (e.g. virtual machines). In this case, `MinKernel` and `MaxKernel` can be set to restrict CPU virtualisation and dummy power management patches to the particular macOS kernel version.
    _Note 2:_ Only the value of `EAX`, which represents the full CPUID, typically needs to be accounted for and remaining bytes should be left as zeroes. The byte order is Little Endian. For example, `C3 06 03 00` stands for CPUID `0x0306C3` (Haswell).
    _Note 3:_ For XCPM support it is recommended to use the following combinations. Be warned that one is required to set the correct [frequency vectors](https://github.com/dortania/bugtracker/issues/190) matching the installed CPU.

    - Haswell-E (`0x0306F2`) to Haswell (`0x0306C3`):
        - `Cpuid1Data`: `C3 06 03 00 00 00 00 00 00 00 00 00 00 00 00 00`
        - `Cpuid1Mask`: `FF FF FF FF 00 00 00 00 00 00 00 00 00 00 00 00`
    
    - Broadwell-E (`0x0406F1`) to Broadwell (`0x0306D4`):
        - `Cpuid1Data`: `D4 06 03 00 00 00 00 00 00 00 00 00 00 00 00 00`
        - `Cpuid1Mask`: `FF FF FF FF 00 00 00 00 00 00 00 00 00 00 00 00`
    
    - Comet Lake U62 (`0x0A0660`) to Comet Lake U42 (`0x0806EC`):
        - `Cpuid1Data`: `EC 06 08 00 00 00 00 00 00 00 00 00 00 00 00 00`
        - `Cpuid1Mask`: `FF FF FF FF 00 00 00 00 00 00 00 00 00 00 00 00`
    
    - Rocket Lake (`0x0A0670`) to Comet Lake (`0x0A0655`):
        - `Cpuid1Data`: `55 06 0A 00 00 00 00 00 00 00 00 00 00 00 00 00`
        - `Cpuid1Mask`: `FF FF FF FF 00 00 00 00 00 00 00 00 00 00 00 00`
    
    - Alder Lake (`0x090672`) to Comet Lake (`0x0A0655`):
        - `Cpuid1Data`: `55 06 0A 00 00 00 00 00 00 00 00 00 00 00 00 00`
        - `Cpuid1Mask`: `FF FF FF FF 00 00 00 00 00 00 00 00 00 00 00 00`
    
    Note 4: Be aware that the following configurations are unsupported by XCPM (at least out of the box):
    
    - Consumer Ivy Bridge (`0x0306A9`) as Apple disabled XCPM for Ivy Bridge and recommends legacy power management for these CPUs. `_xcpm_bootstrap` should manually be patched to enforce XCPM on these CPUs instead of this option.
    - Low-end CPUs (e.g. Haswell+ Pentium) as they are not supported properly by macOS. Legacy workarounds for older models can be found in the `Special NOTES` section of [acidanthera/bugtracker#365](https://github.com/acidanthera/bugtracker/issues/365).

2. `Cpuid1Mask`
    - **Type:** `plist data`, 16 bytes
    - **Failsafe:** All zero
    - **Description:** Bit mask of active bits in `Cpuid1Data`.
    
    When each `Cpuid1Mask` bit is set to `0`, the original CPU bit is used, otherwise set bits take the value of `Cpuid1Data`.

3. `DummyPowerManagement`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.4-12
    - **Description:** Disables `AppleIntelCpuPowerManagement`.
    
    _Note 1:_ This option is a preferred alternative to `NullCpuPowerManagement.kext` for CPUs without native power management driver in macOS.
    
    _Note 2:_ While this option is typically needed to disable `AppleIntelCpuPowerManagement` on unsupported platforms, it can also be used to disable this kext in other situations (e.g. with `Cpuid1Data` left blank).

4. `MaxKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Emulates CPUID and applies `DummyPowerManagement` on specified macOS version or older.
    
    _Note:_ Refer to the [Add MaxKernel]({%link docs/Kernel/add.md %}) description for matching logic.

5. `MinKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Emulates CPUID and applies `DummyPowerManagement` on specified macOS version or newer.
    
    _Note:_ Refer to the [Add MaxKernel]({%link docs/Kernel/add.md %}) description for matching logic.