---
layout: default
title: DataHub Properties
parent: PlatformInfo
nav_order: 3
---

# DataHub Properties

1. `PlatformName`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `name` in `gEfiMiscSubClassGuid`. The value found on Macs is `platform` in ASCII.

2. `SystemProductName`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `Model` in `gEfiMiscSubClassGuid`. The value found on Macs is equal to SMBIOS `SystemProductName` in Unicode.

3. `SystemSerialNumber`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `SystemSerialNumber` in `gEfiMiscSubClassGuid`. The value found on Macs is equal to SMBIOS `SystemSerialNumber` in Unicode.

4. `SystemUUID`
    - **Type:** `plist string`, GUID
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `system-id` in `gEfiMiscSubClassGuid`. The value found on Macs is equal to SMBIOS `SystemUUID` (with swapped byte order).

5. `BoardProduct`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `board-id` in `gEfiMiscSubClassGuid`. The value found on Macs is equal to SMBIOS `BoardProduct` in ASCII.

6. `BoardRevision`
    - **Type:** `plist data`, 1 byte
    - **Failsafe:** `0`
    - **Description:** Sets `board-rev` in `gEfiMiscSubClassGuid`. The value found on Macs seems to correspond to internal board revision (e.g. `01`).

7. `StartupPowerEvents`
    - **Type:** `plist integer`, 64-bit
    - **Failsafe:** `0`
    - **Description:** Sets `StartupPowerEvents` in `gEfiMiscSubClassGuid`. The value found on Macs is power management state bitmask, normally `0`. Known bits read by `X86PlatformPlugin.kext`:

    - `0x00000001` — Shutdown cause was a `PWROK` event (Same as `GEN_PMCON_2` bit `0`)
    - `0x00000002` — Shutdown cause was a `SYS_PWROK` event (Same as `GEN_PMCON_2` bit `1`)
    - `0x00000004` — Shutdown cause was a `THRMTRIP#` event (Same as `GEN_PMCON_2` bit `3`)
    - `0x00000008` — Rebooted due to a `SYS_RESET#` event (Same as `GEN_PMCON_2` bit `4`)
    - `0x00000010` — Power Failure (Same as `GEN_PMCON_3` bit `1` `PWR_FLR`)
    - `0x00000020` — Loss of RTC Well Power (Same as `GEN_PMCON_3` bit `2` `RTC_PWR_STS`)
    - `0x00000040` — General Reset Status (Same as `GEN_PMCON_3` bit `9` `GEN_RST_STS`)
    - `0xffffff80` — SUS Well Power Loss (Same as `GEN_PMCON_3` bit `14`)
    - `0x00010000` — Wake cause was a ME Wake event (Same as `PRSTS` bit `0`, `ME_WAKE_STS`)
    - `0x00020000` — Cold Reboot was ME Induced event (Same as `PRSTS` bit `1` `ME_HRST_COLD_STS`)
    - `0x00040000` — Warm Reboot was ME Induced event (Same as `PRSTS` bit `2` `ME_HRST_WARM_STS`) 
    - `0x00080000` — Shutdown was ME Induced event (Same as `PRSTS` bit `3` `ME_HOST_PWRDN`)
    - `0x00100000` — Global reset ME Watchdog Timer event (Same as `PRSTS` bit `6`)
    - `0x00200000` — Global reset PowerManagement Watchdog Timer event (Same as `PRSTS` bit `15`)

8. `InitialTSC`
    - **Type:** `plist integer`, 64-bit
    - **Failsafe:** `0`
    - **Description:** Sets `InitialTSC` in `gEfiProcessorSubClassGuid`. Sets initial TSC value, normally `0`.

9. `FSBFrequency`
    - **Type:** `plist integer`, 64-bit
    - **Failsafe:** `0` (Automatic)
    - **Description:** Sets `FSBFrequency` in `gEfiProcessorSubClassGuid`.
    
    Sets CPU FSB frequency. This value equals to CPU nominal frequency divided by CPU maximum bus ratio and is specified in Hz. Refer to `MSR_NEHALEM_PLATFORM_INFO` (`CEh`) MSR value to determine maximum bus ratio on modern Intel CPUs.

    _Note:_ This value is not used on Skylake and newer but is still provided to follow suit.

10. `ARTFrequency`
    - **Type:** `plist integer`, 64-bit
    - **Failsafe:** `0` (Automatic)
    - **Description:** Sets `ARTFrequency` in `gEfiProcessorSubClassGuid`.
    
    This value contains CPU ART frequency, also known as crystal clock frequency. Its existence is exclusive to the Skylake generation and newer. The value is specified in Hz, and is normally 24 MHz for the client Intel segment, 25 MHz for the server Intel segment, and 19.2 MHz for Intel Atom CPUs. macOS till 10.15 inclusive assumes 24 MHz by default.
    Note: On Intel Skylake X ART frequency may be a little less (approx. 0.25%) than 24 or 25 MHz due to special EMI-reduction circuit as described in [Acidanthera Bugtracker](https://github.com/acidanthera/bugtracker/issues/448#issuecomment-524914166).

11. `DevicePathsSupported`
    - **Type:** `plist integer`, 32-bit
    - **Failsafe:** `0` (Not installed)
    - **Description:** Sets `DevicePathsSupported` in `gEfiMiscSubClassGuid`. Must be set to `1` for AppleACPIPlatform.kext to append SATA device paths to `Boot####` and `efi-boot-device-data` variables. Set to `1` on all modern Macs.

12. `SmcRevision`
    - **Type:** `plist data`, 6 bytes
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `REV` in `gEfiMiscSubClassGuid`. Custom property read by `VirtualSMC` or `FakeSMC` to generate SMC `REV` key.

13. `SmcBranch`
    - **Type:** `plist data`, 8 bytes
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `RBr` in `gEfiMiscSubClassGuid`. Custom property read by `VirtualSMC` or `FakeSMC` to generate SMC `RBr` key.

14. `SmcPlatform`
    - **Type:** `plist data`, 8 bytes
    - **Failsafe:** Empty (Not installed)
    - **Description:** Sets `RPlt` in `gEfiMiscSubClassGuid`. Custom property read by `VirtualSMC` or `FakeSMC` to generate SMC `RPlt` key.