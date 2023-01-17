---
layout: default
title: Quirks
parent: Kernel
nav_order: 8
---

# Quirks

1. `AppleCpuPmCfgLock`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.4-12
    - **Description:** Disables `PKG_CST_CONFIG_CONTROL` (0xE2) MSR modification in `AppleIntelCPUPowerManagement.kext`, commonly causing early kernel panic, when it is locked from writing.

    Some types of firmware lock the `PKG_CST_CONFIG_CONTROL` MSR register and the bundled `ControlMsrE2` tool can be used to check its state. Note that some types of firmware only have this register locked on some cores. As modern firmware provide a `CFG Lock` setting that allows configuring the `PKG_CST_CONFIG_CONTROL` MSR register lock, this option should be avoided whenever possible.

    On APTIO firmware that do not provide a `CFG Lock` setting in the GUI, it is possible to access the option directly:
        
    1. Download [UEFITool](https://github.com/LongSoft/UEFITool/releases) and [IFR-Extractor](https://github.com/LongSoft/Universal-IFR-Extractor/releases).
    2. Open the firmware image in UEFITool and find `CFG Lock` unicode string. If it is not present, the firmware
    may not have this option and the process should therefore be discontinued.
    3. Extract the `Setup.bin` PE32 Image Section (the UEFITool found) through the `Extract Body` menu option.
    4. Run IFR-Extractor on the extracted file (e.g. `./ifrextract Setup.bin Setup.txt`).
    5. Find `CFG Lock, VarStoreInfo (VarOffset/VarName):` in `Setup.txt` and remember the offset right after
    it (e.g. `0x123`).
    6. Download and run [Modified GRUB Shell](http://brains.by/posts/bootx64.7z) compiled by [brainsucker](https://habr.com/geektimes/post/258090) or use a [newer version](https://github.com/datasone/grub-mod-setup_var) by [datasone](https://github.com/datasone).
    7. Enter `setup_var 0x123 0x00` command, where `0x123` should be replaced by the actual offset, and reboot.
    
    **Warning:** Variable offsets are unique not only to each motherboard but even to its firmware version. Never ever try to use an offset without checking.
    
    On selected platforms, the `ControlMsrE2` tool can also change such hidden options. Pass desired argument: `lock`, `unlock` for `CFG Lock`. Or pass `interactive` to find and modify other hidden options.
    
    As a last resort, consider [patching the BIOS](https://github.com/LongSoft/UEFITool/blob/master/UEFIPatch/patches.txt) (for advanced users only).

2. `AppleXcpmCfgLock`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.8 (not required for older)
    - **Description:** Disables `PKG_CST_CONFIG_CONTROL` (`0xE2`) MSR modification in XNU kernel, commonly causing early kernel panic, when it is locked from writing (XCPM power management).
    
    _Note:_ This option should be avoided whenever possible. Refer to the `AppleCpuPmCfgLock` description for details.

3. `AppleXcpmExtraMsrs`
    - **Type:**`plist boolean`
    - **Failsafe:**`false`
    - **Requirement:**10.8 (not required for older)
    - **Description:**Disables multiple MSR access critical for certain CPUs, which have no native XCPM support.
    
    This is typically used in conjunction with the `Emulate` section on Haswell-E, Broadwell-E, Skylake-SP, and similar CPUs. More details on the XCPM patches are outlined in [acidanthera/bugtracker#365](https://github.com/acidanthera/bugtracker/issues/365).
    
    _Note:_ Additional not provided patches will be required for Ivy Bridge or Pentium CPUs. It is recommended to use `AppleIntelCpuPowerManagement.kext` for the former.

4. `AppleXcpmForceBoost`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.8 (not required for older)
    - **Description:** Forces maximum performance in XCPM mode.
    
    This patch writes `0xFF00` to `MSR_IA32_PERF_CONTROL` (`0x199`), effectively setting maximum multiplier for all the time.
    
    _Note:_ While this may increase the performance, this patch is strongly discouraged on all systems but those explicitly dedicated to scientific or media calculations. Only certain Xeon models typically benefit from the patch.

5. `CustomPciSerialDevice`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.7
    - **Description:** Performs change of PMIO register base address on a customised PCI serial device.
    
    The patch changes the PMIO register base address that the XNU kernel uses for serial input and output, from that of the default built-in COM1 serial port `0x3F8`, to the base address stored in the first IO BAR of a specified PCI device or to a specific base address (e.g. `0x2F8` for COM2).

    _Note:_ By default, serial logging is disabled. `serial=3` boot argument, which enables serial input and output, should be used for XNU to print logs to the serial port.
    
    _Note 2:_ In addition to this patch, kext `Apple16X50PCI0` should be prevented from attaching to have `kprintf` method working properly. This can be achieved by using [PCIeSerialDisable](https://github.com/joevt/PCIeSerialDisable). In addition, for certain Thunderbolt cards the IOKit personality `IOPCITunnelCompatible` also needs to be set to true, which can be done by the `PCIeSerialThunderboltEnable.kext` attached at [acidanthera/bugtracker#2003](https://github.com/acidanthera/bugtracker/issues/2003#issuecomment-1116761087).
    
    _Note 3:_ For this patch to be correctly applied, `Override` must be enabled with all keys properly set in `Custom`, under section `Misc->Serial`.

    _Note 4:_ This patch is for PMIO support and is therefore not applied if `UseMmio` under section `Misc->Serial->Custom` is false. For MMIO, there are boot arguments `pcie_mmio_uart=ADDRESS` and `mmio_uart=ADDRESS` that allow the kernel to use MMIO for serial port access.
    
    _Note 5:_ The serial baud rate must be correctly set in both `BaudRate` under section `Misc->Serial->Custom` and via `serialbaud=VALUE` boot argument, both of which should match against each other. The default baud rate is `115200`.

6. `CustomSMBIOSGuid`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.4
    - **Description:** Performs GUID patching for `UpdateSMBIOSMode Custom` mode. Usually relevant for Dell laptops.

7. `DisableIoMapper`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.8 (not required for older)
    - **Description:** Disables `IOMapper` support in XNU (VT-d), which may conflict with the firmware implementation.
    
    _Note 1:_ This option is a preferred alternative to deleting `DMAR` ACPI table and disabling VT-d in firmware preferences, which does not obstruct VT-d support in other systems in case they need this.
    
    _Note 2:_ Misconfigured IOMMU in the firmware may result in broken devices such as ethernet or Wi-Fi adapters. For instance, an ethernet adapter may cycle in link-up link-down state infinitely and a Wi-Fi adapter may fail to discover networks. Gigabyte is one of the most common OEMs with these issues.

8. `DisableLinkeditJettison`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 11
    - **Description:** Disables `__LINKEDIT` jettison code.
    
    This option lets `Lilu.kext`, and possibly other kexts, function in macOS Big Sur at their best performance levels without requiring the `keepsyms=1` boot argument.

9. `DisableRtcChecksum`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.4
    - **Description:** Disables primary checksum (`0x58-0x59`) writing in AppleRTC.
    
    _Note 1:_ This option will not protect other areas from being overwritten, see [RTCMemoryFixup](https://github.com/acidanthera/RTCMemoryFixup) kernel extension if this is desired.
    _Note 2:_ This option will not protect areas from being overwritten at firmware stage (e.g. macOS bootloader), see `AppleRtcRam` protocol description if this is desired.

10. `ExtendBTFeatureFlags`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.8-11
    - **Description:** Set `FeatureFlags` to `0x0F` for full functionality of Bluetooth, including Continuity.
    
    _Note:_ This option is a substitution for BT4LEContinuityFixup.kext, which does not function properly due to late patching progress.

11. `ExternalDiskIcons`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.4
    - **Description:** Apply icon type patches to AppleAHCIPort.kext to force internal disk icons for all AHCI disks.

    _Note:_ This option should be avoided whenever possible. Modern firmware typically have compatible AHCI controllers.

12. `ForceAquantiaEthernet`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.15.4
    - **Description:** Enable Aquantia AQtion based 10GbE network cards support.
    
    This option enables Aquantia AQtion based 10GbE network cards support, which used to work natively before macOS 10.15.4.
    
    _Note:_ In order for Aquantia cards to properly function, `DisableIoMapper` must be disabled, `DMAR` ACPI table must not be dropped, and `VT-d` must be enabled in BIOS.
    _Note 2:_ While this patch should enable ethernet support for all Aquantia AQtion series, it has only been tested on AQC-107s based 10GbE network cards.
    _Note 3:_ To address `AppleVTD` incompatibilities after applying this quirk, the Reserved Memory Region section of the corresponding device in the `DMAR` ACPI table might be removed. This table should be disassembled and edited, then recompiled to `AML` with tool `iASL`. For the patched `DMAR` table to be [added]({%link docs/ACPI/add.md %}), the original one should be [deleted]({%link docs/ACPI/delete.md %}). More details can be found at [comment on commit 2441455](https://github.com/acidanthera/OpenCorePkg/commit/24414555f2c07e06a3674ec7a2aa1ce4860bbcc7#commitcomment-70530145).

13. `ForceSecureBootScheme`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 11
    - **Description:** Force x86 scheme for IMG4 verification.
    
    _Note:_ This option is required on virtual machines when using `SecureBootModel` different from `x86legacy`.

14. `IncreasePciBarSize`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.10
    - **Description:** Allows IOPCIFamily to boot with 2 GB PCI BARs.
    
    Normally macOS restricts PCI BARs to 1 GB. Enabling this option (still) does not let macOS actually use PCI devices with larger BARs.
    
    _Note:_ This option should be avoided whenever possible. A need for this option indicates misconfigured or defective firmware.

15. `LapicKernelPanic`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.6 (64-bit)
    - **Description:** Disables kernel panic on LAPIC interrupts.

16. `LegacyCommpage`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.4 - 10.6
    - **Description:** Replaces the default 64-bit commpage bcopy implementation with one that does not require SSSE3, useful for legacy platforms. This prevents a `commpage no match` for last panic due to no available 64-bit bcopy functions that do not require SSSE3.

17. `PanicNoKextDump`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.13 (not required for older)
    - **Description:** Prevent kernel from printing kext dump in the panic log preventing from observing panic details. Affects 10.13 and above.

18. `PowerTimeoutKernelPanic`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.15 (not required for older)
    - **Description:** Disables kernel panic on setPowerState timeout.
    
    An additional security measure was added to macOS Catalina (10.15) causing kernel panic on power change timeout for Apple drivers. Sometimes it may cause issues on misconfigured hardware, notably digital audio, which sometimes fails to wake up. For debug kernels `setpowerstate_panic=0` boot argument should be used, which is otherwise equivalent to this quirk.

19. `ProvideCurrentCpuInfo`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.4 (10.14)
    - **Description:** Provides current CPU info to the kernel.
    
    This quirk works differently depending on the CPU:
    - For Microsoft Hyper-V it provides the correct TSC and FSB values to the kernel, as well as disables CPU topology validation (10.8+).
    - For KVM and other hypervisors it provides precomputed MSR 35h values solving kernel panic with `-cpu host`.
    - For Intel CPUs it adds support for asymmetrical SMP systems (e.g. Intel Alder Lake) by patching core count to thread count along with the supplemental required changes (10.14+). Cache size and cache line size are also provided when using 10.4 as Intel Penryn and newer may only have cache information in CPUID leaf 0x4 which is unsupported by 10.4.

20. `SetApfsTrimTimeout`
    - **Type:** plist integer
    - **Failsafe:** -1
    - **Requirement:** 10.14 (not required for older)
    - **Description:** Set trim timeout in microseconds for APFS filesystems on SSDs.
    
    The APFS filesystem is designed in a way that the space controlled via the spaceman structure is either used or free. This may be different in other filesystems where the areas can be marked as used, free, and _unmapped_. All free space is trimmed (unmapped/deallocated) at macOS startup. The trimming procedure for NVMe drives happens in LBA ranges due to the nature of the `DSM` command with up to `256` ranges per command. The more fragmented the memory on the drive is, the more commands are necessary to trim all the free space.
    
    Depending on the SSD controller and the level of drive fragmenation, the trim procedure may take a considerable amount of time, causing noticeable boot slowdown. The APFS driver explicitly ignores previously unmapped areas and repeatedly trims them on boot. To mitigate against such boot slowdowns, the macOS driver introduced a timeout (`9.999999` seconds) that stops the trim operation when not finished in time.
    
    On several controllers, such as Samsung, where the deallocation process is relatively slow, this timeout can be reached very quickly. Essentially, it means that the level of fragmentation is high, thus macOS will attempt to trim the same lower blocks that have previously been deallocated, but never have enough time to deallocate higher blocks. The outcome is that trimming on such SSDs will be non-functional soon after installation, resulting in additional wear on the flash.
    
    One way to workaround the problem is to increase the timeout to an extremely high value, which at the cost of slow boot times (extra minutes) will ensure that all the blocks are trimmed. Setting this option to a high value, such as `4294967295` ensures that all blocks are trimmed. Alternatively, use over-provisioning, if supported, or create a dedicated unmapped partition where the reserve blocks can be found by the controller. Conversely, the trim operation can be mostly disabled by setting a very low timeout value, while 0 entirely disables it. Refer to this [article](https://interface31.ru/tech_it/2015/04/mozhno-li-effektivno-ispolzovat-ssd-bez-podderzhki-trim.html) for details.
    
    _Note:_ The failsafe value `-1` indicates that this patch will not be applied, such that `apfs.kext` will remain untouched.
    
    _Note 2:_ On macOS 12.0 and above, it is no longer possible to specify trim timeout. However, trim can be disabled by setting `0`.
    
    Note 3 : Trim operations are only affected at booting phase when the startup volume is mounted. Either specifying timeout, or completely disabling trim with `0`, will not affect normal macOS running.

21. `ThirdPartyDrives`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.6 (not required for older)
    - **Description:** Apply vendor patches to IOAHCIBlockStorage.kext to enable native features for third-party drives, such as TRIM on SSDs or hibernation support on 10.15 and newer.
    
    _Note:_ This option may be avoided on user preference. NVMe SSDs are compatible without the change. For AHCI SSDs on modern macOS version there is a dedicated built-in utility called `trimforce`. Starting from 10.15 this utility creates `EnableTRIM` variable in `APPLE_BOOT_VARIABLE_GUID` namespace with `01 00 00 00` value.

22. `XhciPortLimit`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Requirement:** 10.11 (not required for older)
    - **Description:** Patch various kexts (AppleUSBXHCI.kext, AppleUSBXHCIPCI.kext, IOUSBHostFamily.kext) to remove USB port count limit of 15 ports.
    
    _Note:_ This option should be avoided whenever possible. USB port limit is imposed by the amount of used bits in locationID format and there is no possible way to workaround this without heavy OS modification. The only valid solution is to limit the amount of used ports to 15 (discarding some). More details can be found on [AppleLife.ru](https://applelife.ru/posts/550233).