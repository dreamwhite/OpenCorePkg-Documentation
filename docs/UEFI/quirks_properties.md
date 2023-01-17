---
layout: default
title: Quirks Properties
nav_order: 18
parent: UEFI
---

# Quirks Properties

1. `ActivateHpetSupport`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Activates HPET support.

    Older boards like ICH6 may not always have HPET setting in the firmware preferences, this option tries to force enable it.

2. `EnableVectorAcceleration`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable AVX vector acceleration of SHA-512 and SHA-384 hashing algorithms.
    
    _Note:_ This option may cause issues on certain laptop firmwares, including Lenovo.

3. `EnableVmx`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable Intel virtual machine extensions.

    _Note:_ Required to allow virtualization in Windows on some Mac hardware. VMX is enabled or disabled and locked by BIOS before OpenCore starts on most firmware. Use BIOS to enable virtualization where possible.

4. `DisableSecurityPolicy`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Disable platform security policy.

    _Note:_ This setting disables various security features of the firmware, defeating the purpose of any kind of Secure Boot. Do NOT enable if using UEFI Secure Boot.

5. `ExitBootServicesDelay`
    - **Type:** `plist integer`
    - **Failsafe:** 0
    - **Description:** Adds delay in microseconds after `EXIT_BOOT_SERVICES` event.

    This is a very rough workaround to circumvent the `Still waiting for root device` message on some APTIO IV firmware (ASUS Z87-Pro) particularly when using FileVault 2. It appears that for some reason, they execute code in parallel to `EXIT_BOOT_SERVICES`, which results in the SATA controller being inaccessible from macOS. A better approach is required and Acidanthera is open to suggestions. Expect 3 to 5 seconds to be adequate when this quirk is needed.

6. `ForceOcWriteFlash`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enables writing to flash memory for all OpenCore-managed NVRAM system variables.

    _Note:_ This value should be disabled on most types of firmware but is left configurable to account for firmware that may have issues with volatile variable storage overflows or similar. Boot issues across multiple OSes can be observed on e.g. Lenovo Thinkpad T430 and T530 without this quirk. Apple variables related to Secure Boot and hibernation are exempt from this for security reasons. Furthermore, some OpenCore variables are exempt for different reasons, such as the boot log due to an available user option, and the TSC frequency due to timing issues. When toggling this option, a NVRAM reset may be required to ensure full functionality.

7. `ForgeUefiSupport`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Implement partial UEFI 2.x support on EFI 1.x firmware.

    This setting allows running some software written for UEFI 2.x firmware, such as NVIDIA GOP Option ROMs, on hardware with older EFI 1.x firmware (e.g. `MacPro5,1`).

8. `IgnoreInvalidFlexRatio`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Some types of firmware (such as APTIO IV) may contain invalid values in the `MSR_FLEX_RATIO` (`0x194`) MSR register. These values may cause macOS boot failures on Intel platforms.

    _Note:_ While the option is not expected to harm unaffected firmware, its use is recommended only when specifically required.

9. `ReleaseUsbOwnership`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Attempt to detach USB controller ownership from the firmware driver. While most types of firmware manage to do this properly, or at least have an option for this, some do not. As a result, the operating system may freeze upon boot. Not recommended unless specifically required.

10. `ReloadOptionRoms`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Query PCI devices and reload their Option ROMs if available.

    For example, this option allows reloading NVIDIA GOP Option ROM on older Macs after the firmware version is upgraded via `ForgeUefiSupport`.

11. `RequestBootVarRouting`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Request redirect of all Boot prefixed variables from `EFI_GLOBAL_VARIABLE_GUID` to `OC_VENDOR_VARIABLE_GUID`.

    This quirk requires `OC_FIRMWARE_RUNTIME` protocol implemented in `OpenRuntime.efi`. The quirk lets default boot entry preservation at times when the firmware deletes incompatible boot entries. In summary, this quirk is required to reliably use the [Startup Disk](https://support.apple.com/HT202796) preference pane in firmware that is not compatible with macOS boot entries by design.
    
    By redirecting Boot prefixed variables to a separate GUID namespace with the help of `RequestBootVarRouting` quirk we achieve multiple goals:
    - Operating systems are jailed and only controlled by OpenCore boot environment to enhance security.
    - Operating systems do not mess with OpenCore boot priority, and guarantee fluent updates and hibernation
    wakes for cases that require reboots with OpenCore in the middle.
    - Potentially incompatible boot entries, such as macOS entries, are not deleted or corrupted in any way.

12. `ResizeGpuBars`
    - **Type:** `plist integer`
    - **Failsafe:** -1
    - **Description:** Configure GPU PCI BAR sizes.

    This quirk sets GPU PCI BAR sizes as specified or chooses the largest available below the `ResizeGpuBars` value. The specified value follows PCI Resizable BAR spec. Use `0` for `1` MB, `1` for 2 MB, `2` for 4 MB, and so on up to `19` for 512 GB.

    Resizable BAR technology allows to ease PCI device programming by mapping a configurable memory region, BAR, into CPU address space (e.g. VRAM to RAM) as opposed to a fixed memory region. This technology is necessary, because one cannot map the largest memory region by default, for the reasons of backwards compatibility with older hardware not supporting 64-bit BARs. Consequentially devices of the last decade use BARs up to 256 MB by default (4 remaining bits are used by other data) but generally allow resizing them to both smaller and larger powers of two (e.g. from 1 MB up to VRAM size).

    Operating systems targeting x86 platforms generally do not control PCI address space, letting UEFI firmware decide on the BAR addresses and sizes. This illicit practice resulted in Resizable BAR technology being unused up until 2020 despite being standardised in 2008 and becoming widely available in the hardware soon after.
    
    Modern UEFI firmware allow the use of Resizable BAR technology but generally restrict the configurable options to failsafe default (OFF) and maximum available (ON). This quirk allows to fine-tune this value for testing and development purposes.
    
    Consider a GPU with 2 BARs:
    - `BAR0` supports sizes from 256 MB to 8 GB. Its value is 4 GB.
    - `BAR1` supports sizes from 2 MB to 256 MB. Its value is 256 MB.

    _Example 1:_ Setting `ResizeGpuBars` to 1 GB will change `BAR0` to 1 GB and leave `BAR1` unchanged.
    _Example 2:_ Setting `ResizeGpuBars` to 1 MB will change `BAR0` to 256 MB and `BAR0` to 2 MB.
    _Example 3:_ Setting `ResizeGpuBars` to 16 GB will change `BAR0` to 8 GB and leave `BAR1` unchanged.

    _Note 1:_ This quirk shall not be used to workaround macOS limitation to address BARs over 1 GB. `ResizeAppleGpuBars` should be used instead.
    
    _Note 2:_ While this quirk can increase GPU PCI BAR sizes, this will not work on most firmware as is, because the quirk does not relocate BARs in memory, and they will likely overlap. Contributions to improve this feature are welcome.

13. `TscSyncTimeout`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Attempts to perform TSC synchronisation with a specified timeout.

    The primary purpose of this quirk is to enable early bootstrap TSC synchronisation on some server and laptop models when running a debug XNU kernel. For the debug kernel the TSC needs to be kept in sync across the cores before any kext could kick in rendering all other solutions problematic. The timeout is specified in microseconds and depends on the amount of cores present on the platform, the recommended starting value is `500000`.

    This is an experimental quirk, which should only be used for the aforementioned problem. In all other cases, the quirk may render the operating system unstable and is not recommended. The recommended solution in the other cases is to install a kernel extension such as [VoodooTSCSync](https://github.com/RehabMan/VoodooTSCSync), [TSCAdjustReset](https://github.com/interferenc/TSCAdjustReset), or [CpuTscSync](https://github.com/lvs1974/CpuTscSync) (a more specialised variant of VoodooTSCSync for newer laptops).
    
    _Note:_ This quirk cannot replace the kernel extension because it cannot operate in ACPI S3 (sleep wake) mode and because the UEFI firmware only provides very limited multicore support which prevents precise updates of the MSR registers.

14. `UnblockFsConnect`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Some types of firmware block partition handles by opening them in `By Driver` mode, resulting in an inability to install File System protocols.

    _Note:_ This quirk is useful in cases where unsuccessful drive detection results in an absence of boot entries.