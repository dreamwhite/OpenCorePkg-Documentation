---
layout: default
title: Quirks
parent: Booter
nav_order: 5
---

# Quirks

1. `AllowRelocationBlock`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Allows booting macOS through a relocation block.

    The relocation block is a scratch buffer allocated in the lower 4 GB used for loading the kernel and related structures by EfiBoot on firmware where the lower memory region is otherwise occupied by (assumed) non-runtime data. Right before kernel startup, the relocation block is copied back to lower addresses. Similarly, all the other addresses pointing to the relocation block are also carefully adjusted. The relocation block can be used when:

    - No better slide exists (all the memory is used)
    - `slide=0` is forced (by an argument or safe mode)
    - KASLR (slide) is unsupported (this is macOS 10.7 or older)

    This quirk requires `ProvideCustomSlide` to be enabled and typically also requires enabling `AvoidRuntimeDefrag` to function correctly. Hibernation is not supported when booting with a relocation block, which will only be used if required when the quirk is enabled.
    Note: While this quirk is required to run older macOS versions on platforms with used lower memory, it is not compatible with some hardware and macOS 11. In such cases, consider using `EnableSafeModeSlide` instead.

2. `AvoidRuntimeDefrag`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Protect from boot.efi runtime memory defragmentation.

    This option fixes UEFI runtime services (date, time, NVRAM, power control, etc.) support on firmware that uses SMM backing for certain services such as variable storage. SMM may try to access memory by physical addresses in non-SMM areas but this may sometimes have been moved by boot.efi. This option prevents boot.efi from moving such data.
    
    _Note:_ Most types of firmware, apart from Apple and VMware, need this quirk.

3. `DevirtualiseMmio`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Remove runtime attribute from certain MMIO regions.

    This quirk reduces the stolen memory footprint in the memory map by removing the runtime bit for known memory regions. This quirk may result in an increase of KASLR slides available but without additional measures, it is not necessarily compatible with the target board. This quirk typically frees between 64 and 256 megabytes of memory, present in the debug log, and on some platforms, is the only way to boot macOS, which otherwise fails with allocation errors at the bootloader stage.
    This option is useful on all types of firmware, except for some very old ones such as Sandy Bridge. On certain firmware, a list of addresses that need virtual addresses for proper NVRAM and hibernation functionality may be required. Use the `MmioWhitelist` section for this.

4. `DisableSingleUser`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Disable single user mode.
    This is a security option that restricts the activation of single user mode by ignoring the `CMD+S` hotkey and the `-s` boot argument. The behaviour with this quirk enabled is supposed to match T2-based model behaviour. Refer to this [archived article](https://web.archive.org/web/20200517125051/https://support.apple.com/en-us/HT201573) to understand how to use single user mode with this quirk enabled.

5. `DisableVariableWrite`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Protect from macOS NVRAM write access.

    This is a security option that restricts NVRAM access in macOS. This quirk requires `OC_FIRMWARE_RUNTIME` protocol implemented in `OpenRuntime.efi`.

    _Note:_ This quirk can also be used as an ad hoc workaround for defective UEFI runtime services implementations that are unable to write variables to NVRAM and results in operating system failures.

6. `DiscardHibernateMap`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Reuse original hibernate memory map.

    This option forces the XNU kernel to ignore a newly supplied memory map and assume that it did not change after waking from hibernation. This behaviour is required by Windows to work. Windows mandates [preserving](https://docs.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-uefi#hibernation-state-s4-transition-requirements) runtime memory size and location after S4 wake.

    _Note:_ This may be used to workaround defective memory map implementations on older, rare legacy hardware. Examples of such hardware are Ivy Bridge laptops with Insyde firmware such as the Acer V3-571G. Do not use this option without a full understanding of the implications.

7. `EnableSafeModeSlide`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Patch bootloader to have KASLR enabled in safe mode.
    This option is relevant to users with issues booting to safe mode (e.g. by holding `shift` or with using the `-x` boot argument). By default, safe mode forces `0` slide as if the system was launched with the `slide=0` boot argument.

    - This quirk attempts to patch the `boot.efi` file to remove this limitation and to allow using other values (from `1` to `255` inclusive).
    - This quirk requires enabling `ProvideCustomSlide`.

    _Note:_ The need for this option is dependent on the availability of safe mode. It can be enabled when booting to safe mode fails.

8. `EnableWriteUnprotector`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Permit write access to UEFI runtime services code.

    This option bypasses `WˆX` permissions in code pages of UEFI runtime services by removing write protection (`WP`) bit from `CR0` register during their execution. This quirk requires `OC_FIRMWARE_RUNTIME` protocol implemented in `OpenRuntime.efi`.

    _Note:_ This quirk may potentially weaken firmware security. Please use `RebuildAppleMemoryMap` if the firmware supports memory attributes table (MAT). Refer to the `OCABC: MAT support is 1/0` log entry to determine whether MAT is supported.

9. `ForceBooterSignature`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set macOS boot-signature to OpenCore launcher.

    Booter signature, essentially a SHA-1 hash of the loaded image, is used by Mac EFI to verify the authenticity of the bootloader when waking from hibernation. This option forces macOS to use OpenCore launcher SHA-1 hash as a booter signature to let OpenCore shim hibernation wake on Mac EFI firmware.
    
    _Note:_ OpenCore launcher path is determined from `LauncherPath` property.

10. `ForceExitBootServices`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Retry `ExitBootServices` with new memory map on failure.
    
    Try to ensure that the `ExitBootServices` call succeeds. If required, an outdated MemoryMap key argument can be used by obtaining the current memory map and retrying the `ExitBootServices` call.
    
    _Note:_ The need for this quirk is determined by early boot crashes of the firmware. Do not use this option without a full understanding of the implications.

11. `ProtectMemoryRegions`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Protect memory regions from incorrect access.

    Some types of firmware incorrectly map certain memory regions:
    
    - The CSM region can be marked as boot services code, or data, which leaves it as free memory for the XNU kernel.
    - MMIO regions can be marked as reserved memory and stay unmapped. They may however be required to be accessible at runtime for NVRAM support.
    
    This quirk attempts to fix the types of these regions, e.g. ACPI NVS for CSM or MMIO for MMIO.
    
    _Note:_ The need for this quirk is determined by artifacts, sleep wake issues, and boot failures. This quirk is typically only required by very old firmware.

12. `ProtectSecureBoot`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Protect UEFI Secure Boot variables from being written.
    Reports security violation during attempts to write to `db`, `dbx`, `PK`, and `KEK` variables from the operating system.

    _Note:_ This quirk attempts to avoid issues with NVRAM implementations with fragmentation issues, such as on the `MacPro5,1` as well as on certain Insyde firmware without garbage collection or with defective garbage collection.

13. `ProtectUefiServices`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Protect UEFI services from being overridden by the firmware.

    Some modern firmware, including on virtual machines such as VMware, may update pointers to UEFI services during driver loading and related actions. Consequently, this directly obstructs other quirks that affect memory management, such as `DevirtualiseMmio`, `ProtectMemoryRegions`, or `RebuildAppleMemoryMap`, and may also obstruct other quirks depending on the scope of such.

    GRUB shim makes similar on-the-fly changes to various UEFI image services, which are also protected against by this quirk.
    
    _Note 1_: On VMware, the need for this quirk may be determined by the appearance of the “Your Mac OS guest might run unreliably with more than one virtual core.” message.
    
    _Note 2_: This quirk is needed for correct operation if OpenCore is chainloaded from GRUB with BIOS Secure Boot enabled.

14. `ProvideCustomSlide`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Provide custom KASLR slide on low memory.

    This option performs memory map analysis of the firmware and checks whether all slides (from `1` to `255`) can be used. As `boot.efi` generates this value randomly with `rdrand` or pseudo randomly `rdtsc`, there is a chance of boot failure when it chooses a conflicting slide. In cases where potential conflicts exist, this option forces macOS to select a pseudo random value from the available values. This also ensures that the `slide=` argument is never passed to the operating system (for security reasons).

    _Note:_ The need for this quirk is determined by the `OCABC: Only N/256 slide values are usable!` message in the debug log.

15. `ProvideMaxSlide`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Provide maximum KASLR slide when higher ones are unavailable.
    
    This option overrides the maximum slide of 255 by a user specified value between 1 and 254 (inclusive) when `ProvideCustomSlide` is enabled. It is assumed that modern firmware allocates pool memory from top to bottom, effectively resulting in free memory when slide scanning is used later as temporary memory during kernel loading. When such memory is not available, this option stops the evaluation of higher slides.

    _Note:_ The need for this quirk is determined by random boot failures when `ProvideCustomSlide` is enabled and the randomized slide falls into the unavailable range. When `AppleDebug` is enabled, the debug log typically contains messages such as `AAPL: [EB|‘LD:LKC] } Err(0x9)`. To find the optimal value, append `slide=X`, where `X` is the slide value, to the `boot-args` and select the largest one that does not result in boot failures.

16. `RebuildAppleMemoryMap`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Generate macOS compatible Memory Map.

    The Apple kernel has several limitations on parsing the UEFI memory map:

    - The Memory map size must not exceed 4096 bytes as the Apple kernel maps it as a single 4K page. As some types of firmware can have very large memory maps, potentially over 100 entries, the Apple kernel will crash on boot.
    - The Memory attributes table is ignored. `EfiRuntimeServicesCode` memory statically gets `RX` permissions while all other memory types get `RW` permissions. As some firmware drivers may write to global variables at runtime, the Apple kernel will crash at calling UEFI runtime services unless the driver `.data` section has a `EfiRuntimeServicesData` type.

    To workaround these limitations, this quirk applies memory attribute table permissions to the memory map passed to the Apple kernel and optionally attempts to unify contiguous slots of similar types if the resulting memory map exceeds 4 KB.
    
    _Note 1_: Since several types of firmware come with incorrect memory protection tables, this quirk often comes paired with `SyncRuntimePermissions`.

    _Note 2:_ The need for this quirk is determined by early boot failures. This quirk replaces `EnableWriteUnprotector` on firmware supporting Memory Attribute Tables (MAT). This quirk is typically unnecessary when using `OpenDuetPkg` but may be required to boot macOS 10.6, and earlier, for reasons that are as yet unclear.

17. `ResizeAppleGpuBars`
    - **Type:** `plist integer`
    - **Failsafe:** `-1`
    - **Description:** Reduce GPU PCI BAR sizes for compatibility with macOS.
    
    This quirk reduces GPU PCI BAR sizes for Apple macOS up to the specified value or lower if it is unsupported. The specified value follows PCI Resizable BAR spec. While Apple macOS supports a theoretical 1 GB maximum, in practice all non-default values may not work correctly. For this reason the only supported value for this quirk is the minimal supported BAR size, i.e. 0. Use `-1` to disable this quirk.

    For development purposes one may take risks and try other values. Consider a GPU with 2 BARs:
    
    - `BAR0` supports sizes from 256 MB to 8 GB. Its value is 4 GB.
    - `BAR1` supports sizes from 2 MB to 256 MB. Its value is 256 MB.
    
    _Example 1:_ Setting `ResizeAppleGpuBars` to 1 GB will change `BAR0` to 1 GB and leave `BAR1` unchanged.
    _Example 2:_ Setting `ResizeAppleGpuBars` to 1 MB will change `BAR0` to 256 MB and `BAR0` to 2 MB.
    _Example 3:_ Setting `ResizeAppleGpuBars` to 16 GB will make no changes.
    
    _Note:_ See `ResizeGpuBars` quirk for general GPU PCI BAR size configuration and more details about the technology.

18. `SetupVirtualMap`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Setup virtual memory at `SetVirtualAddresses`.

    Some types of firmware access memory by virtual addresses after a `SetVirtualAddresses` call, resulting in early boot crashes. This quirk workarounds the problem by performing early boot identity mapping of assigned virtual addresses to physical memory.

    _Note:_ The need for this quirk is determined by early boot failures.

19. `SignalAppleOS`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Report macOS being loaded through OS Info for any OS.

    This quirk is useful on Mac firmware, which loads different operating systems with different hardware configurations. For example, it is supposed to enable Intel GPU in Windows and Linux in some dual-GPU MacBook models.

20. `SyncRuntimePermissions`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Update memory permissions for the runtime environment.
    
    Some types of firmware fail to properly handle runtime permissions:
    - They incorrectly mark `OpenRuntime` as not executable in the memory map.
    - They incorrectly mark `OpenRuntime` as not executable in the memory attributes table.
    - They lose entries from the memory attributes table after `OpenRuntime` is loaded.
    - They mark items in the memory attributes table as read-write-execute.

    This quirk attempts to update the memory map and memory attributes table to correct this.
    
    _Note:_ The need for this quirk is indicated by early boot failures (note: includes halt at black screen as well as more obvious crash). Particularly likely to affect early boot of Windows or Linux (but not always both) on affected systems. Only firmware released after 2017 is typically affected.