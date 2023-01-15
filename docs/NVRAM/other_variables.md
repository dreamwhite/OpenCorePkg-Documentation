---
layout: default
title: Other Variables
parent: NVRAM
nav_order: 5
---

# Other Variables

The following variables may be useful for certain configurations or troubleshooting:

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:boot-args`

Kernel arguments, used to pass configuration to Apple kernel and drivers. There are many arguments, which may be found by looking for the use of `PE_parse_boot_argn` function in the kernel or driver code. Some of the known boot arguments include:

- `acpi_layer=0xFFFFFFFF`
- `acpi_level=0xFFFF5F` (implies ACPI_ALL_COMPONENTS)
- `arch=i386` (force kernel architecture to `i386`, see `KernelArch`)
- `batman=VALUE` (`AppleSmartBatteryManager` debug mask)
- `batman-nosmc=1` (disable `AppleSmartBatteryManager` SMC interface)
- `cpus=VALUE` (maximum number of CPUs used)
- `debug=VALUE` (debug mask)
- `io=VALUE` (`IOKit` debug mask)
- `ioaccel_debug=VALUE` (`IOAccelerator` debug mask)
- `keepsyms=1` (show panic log debug symbols)
- `kextlog=VALUE` (kernel extension loading debug mask)
- `nvram-log=1` (enables AppleEFINVRAM logs)
- `nv_disable=1` (disables NVIDIA GPU acceleration)
- `nvda_drv=1` (legacy way to enable NVIDIA web driver, removed in 10.12)
- `npci=0x2000` ([legacy](https://www.insanelymac.com/forum/topic/260539-1068-officially-released/?do=findComment&comment=1707972), disables `kIOPCIConfiguratorPFM64`)
- `lapic_dont_panic=1` (disable lapic spurious interrupt panic on AP cores)
- `panic_on_display_hang=1` (trigger panic on display hang)
- `panic_on_gpu_hang=1` (trigger panic on GPU hang)
- `serial=VALUE` (configure serial logging mode) - The following bits are used by XNU:

    * `0x01` (`SERIALMODE_OUTPUT`, bit `0`) — Enable serial output.
    * `0x02` (`SERIALMODE_INPUT`, bit `1`) — Enable serial input.
    * `0x04` (`SERIALMODE_SYNCDRAIN`, bit `2`) — Enable serial drain synchronisation. 
    * `0x08` (`SERIALMODE_BASE_TTY`, bit `3`) — Load Base/Recovery/FVUnlock TTY. * 
    * `0x10` (`SERIALMODE_NO_IOLOG`, bit `4`) — Prevent IOLogs writing to serial.

- `slide=VALUE` (manually set KASLR slide)
- `smcdebug=VALUE` (`AppleSMC` debug mask)
- `spin_wait_for_gpu=1` (reduces GPU timeout on high load)
- `-amd_no_dgpu_accel` (alternative to [WhateverGreen](https://github.com/acidanthera/WhateverGreen)'s `-radvesa` for new GPUs)
- `-nehalem_error_disable` (disables the `AppleTyMCEDriver`)
- `-no_compat_check` (disable model checking on 10.7+)
- `-s` (single mode)
- `-v` (verbose mode)
- `-x` (safe mode)

There are multiple external places summarising macOS argument lists: [example 1](https://osxeon.wordpress.com/2015/08/10/boot-argument-options-in-os-x), [example 2](https://superuser.com/questions/255176/is-there-a-list-of-available-boot-args-for-darwin-os-x).

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:bootercfg`

Booter arguments, similar to `boot-args` but for `boot.efi`. Accepts a set of arguments, which are hexadecimal 64-bit values with or without `0x`. At different stages `boot.efi` will request different debugging (logging) modes (e.g. after `ExitBootServices` it will only print to serial). Several booter arguments control whether these requests will succeed. The list of known requests is covered below:

- `0x00` - `INIT`.
- `0x01` - `VERBOSE` (e.g. `-v`, force console logging).
- `0x02` - `EXIT`.
- `0x03` - `RESET:OK`.
- `0x04` - `RESET:FAIL` (e.g. unknown `board-id`, hibernate mismatch, panic loop, etc.).
- `0x05` - `RESET:RECOVERY`.
- `0x06` - `RECOVERY`.
- `0x07` - `REAN:START`.
- `0x08` - `REAN:END`.
- `0x09` - `DT` (can no longer log to DeviceTree).
- `0x0A` - `EXITBS:START` (forced serial only).
- `0x0B` - `EXITBS:END` (forced serial only).
- `0x0C` - `UNKNOWN`.

In 10.15, debugging support was defective up to the 10.15.4 release due to refactoring issues as well as the introduction of a [new debug protocol](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/Protocol/AppleDebugLog.h). Some of the arguments and their values below may not be valid for versions prior to 10.15.4. The list of known arguments is covered below:

- `boot-save-log=VALUE` — debug log save mode for normal boot.

    * `0`
    * `1`
    * `2` — (default).
    * `3`
    * `4` — (save to file).

- `wake-save-log=VALUE` — debug log save mode for hibernation wake. 
    * `0` — disabled.
    * `1`
    * `2` — (default).
    * `3` — (unavailable).
    * `4` — (save to file, unavailable).

- `breakpoint=VALUE` — enables debug breaks (missing in production `boot.efi`).
    * `0` — disables debug breaks on errors (default).
    * `1` — enables debug breaks on errors.

- `console=VALUE` — enables console logging.
    * `0` — disables console logging.
    * `1` — enables console logging when debug protocol is missing (default).
    * `2` — enables console logging unconditionally (unavailable).

- `embed-log-dt=VALUE` — enables DeviceTree logging.
    * `0` — disables DeviceTree logging (default).
    * `1` — enables DeviceTree logging.

- `kc-read-size=VALUE` — Chunk size used for buffered I/O from network or disk for prelinkedkernel reading and related. Set to 1MB (0x100000) by default, can be tuned for faster booting.

- `log-level=VALUE` — log level bitmask.
    * `0x01` — enables trace logging (default).

- `serial=VALUE` — enables serial logging.
    * `0` — disables serial logging (default).
    * `1` — enables serial logging for EXITBS:END onwards.
    * `2` — enables serial logging for EXITBS:START onwards.
    * `3` — enables serial logging when debug protocol is missing.
    * `4` — enables serial logging unconditionally.

- `timestamps=VALUE` — enables timestamp logging.
    * `0` — disables timestamp logging.
    * `1` — enables timestamp logging (default).

- `log=VALUE` — deprecated starting from 10.15.
    * 1 — AppleLoggingConOutOrErrSet/AppleLoggingConOutOrErrPrint (classical ConOut/StdErr)
    * 2 — AppleLoggingStdErrSet/AppleLoggingStdErrPrint (StdErr or serial?)
    * 4 — AppleLoggingFileSet/AppleLoggingFilePrint (BOOTER.LOG/BOOTER.OLD file on EFI partition)

- `debug=VALUE` — deprecated starting from 10.15.
    * 1 — enables print something to BOOTER.LOG (stripped code implies there may be a crash)
    * 2 — enables perf logging to /efi/debug-log in the device three
    * 4 — enables timestamp printing for styled printf calls
- `level=VALUE` — deprecated starting from 10.15. Verbosity level of DEBUG output. Everything but `0x80000000` is stripped from the binary, and this is the default value.

_Note:_ Enable the `AppleDebug` option to display verbose output from `boot.efi` on modern macOS versions. This will save the log to the general OpenCore log file. For versions before 10.15.4, set `bootercfg` to `log=1`. This will print verbose output onscreen.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:bootercfg-once`
    Booter arguments override removed after first launch. Otherwise equivalent to bootercfg.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:csr-data`
    Specify sources of kexts which will be approved regardless of SIP `CSR_ALLOW_UNAPPROVED_KEXTS` value.

    Example contents:

    `<dict><key>kext-allowed-teams</key><array><string>{DEVELOPER-TEAM-ID}</string></array></dict>%00`

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:efiboot-perf-record`

    Enable performance log saving in `boot.efi`. Performance log is saved to physical memory and is pointed to by the `efiboot-perf-record-data` and `efiboot-perf-record-size` variables. Starting from 10.15.4, it can also be saved to the OpenCore log by setting the `AppleDebug` option.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:fmm-computer-name`

    Current saved host name. ASCII string.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:nvda_drv`

    NVIDIA Web Driver control variable. Takes ASCII digit 1 or 0 to enable or disable installed driver.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:run-efi-updater`

    Override EFI firmware updating support in macOS (MultiUpdater, ThorUtil, and so on). Setting this to `No` or alternative boolean-castable value will prevent any firmware updates in macOS starting with 10.10 at least.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:StartupMute`

    Mute startup chime sound in firmware audio support. 8-bit integer. The value of `0x00` means unmuted. Missing variable or any other value means muted.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:SystemAudioVolume`

    System audio volume level for firmware audio support. 8-bit unsigned integer. The bit of `0x80` means muted. The remaining bits are used to encode the raw amplifier gain setting to be applied to the audio amplifier in use. Exactly what this value means depends on the codec (and potentially on the specific amplifier within the codec). This value is capped by macOS to the `MaximumBootBeepVolume` AppleHDA layout value, to avoid over-loud audio playback in the firmware.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:SystemAudioVolumeDB`

    Current system audio volume level in decibels (dB). 8-bit signed integer. The value represents the audio offset (gain if positive, attenuation if negative) in dB relative to the amplifier reference value of 0 dB. Exactly which volume level is represented by 0 dB depends on the codec (and potentially on the specific amplifier within the codec) but it is normally at or near the maximum available amplifier volume. Typical values of this variable range from approximately -60 (the exact value depends on the audio hardware) up to exactly 0. On non-typical audio hardware, the value may go above zero.

    _Note:_ Unlike `SystemAudioVolume`, this value is not capped.

- `5EDDA193-A070-416A-85EB-2A1181F45B18:PEXConf`

    PCI expansion slot configuration for `MacPro7,1`. 8-byte sequence describing default PCI slot configuration. Each byte refers to a configuration for a dedicated PCI slot.

    - Slot 1 resides at `IOService:/AppleACPIPlatformExpert/PC01@0/AppleACPIPCI/BR1A@0` and its path is hardcoded. This slot is not behind a muxer.
    - Slot 3 resides at `IOService:/AppleACPIPlatformExpert/PC03@0/AppleACPIPCI/BR3A@0` and its path is hardcoded. This slot is not behind a muxer.
    - Slots 2, 4-8 are dynamic and are matched based on `AAPL,slot-name` property with `Slot-N` value, where `N` is the slot number. All these slots are behind the muxer.

    Refer to the [support page](https://support.apple.com/HT210104) for more details on how `MacPro7,1` slots are configured.

- `5EDDA193-A070-416A-85EB-2A1181F45B18:SlotUtilPEXConf`

    User PCI expansion slot configuration for `MacPro7,1`. 8-byte sequence describing user PCI slot configuration.