---
layout: default
title: ProtocolOverrides Properties
nav_order: 17
parent: UEFI
---

# ProtocolOverrides Properties

1. `AppleAudio`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces Apple audio protocols with builtin versions.

    Apple audio protocols allow OpenCore and the macOS bootloader to play sounds and signals for screen reading or audible error reporting. Supported protocols are beep generation and VoiceOver. The VoiceOver protocol is only provided natively by Gibraltar machines (T2), however versions of macOS which support VoiceOver will see and use the implementation provided by OpenCore, on screens such as FileVault 2 unlock. VoiceOver is not supported before macOS High Sierra (10.13). Older macOS versions use the AppleHDA protocol (which is not currently implemented) instead.
    
    Only one set of audio protocols can be available at a time, so this setting should be enabled in order to enable audio playback in the OpenCore user interface on Mac systems implementing some of these protocols.
    
    _Note:_ The backend audio driver needs to be configured in `UEFI Audio` section for these protocols to be able to stream audio.

2. `AppleBootPolicy`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple Boot Policy protocol with a builtin version. This may be used to ensure APFS compatibility on VMs and legacy Macs.
    _Note:_ This option is advisable on certain Macs, such as the `MacPro5,1`, that are APFS compatible but on which the Apple Boot Policy protocol has recovery detection issues.

3. `AppleDebugLog`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple Debug Log protocol with a builtin version.

4. `AppleEg2Info`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple EFI Graphics 2 protocol with a builtin version.

    _Note 1:_ This protocol allows newer `EfiBoot` versions (at least 10.15) to expose screen rotation to macOS. Refer to `ForceDisplayRotationInEFI` variable description on how to set screen rotation angle.
    
    _Note 2:_ On systems without native support for `ForceDisplayRotationInEFI`, `DirectGopRendering=true` is also required for this setting to have an effect.

5. `AppleFramebufferInfo`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple Framebuffer Info protocol with a builtin version. This may be used to override framebuffer information on VMs and legacy Macs to improve compatibility with legacy EfiBoot such as the one in macOS 10.4.
    
    _Note:_ The current implementation of this property results in it only being active when GOP is available (it is always equivalent to `false` otherwise).

6. `AppleImageConversion`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple Image Conversion protocol with a builtin version.

7. `AppleImg4Verification`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple IMG4 Verification protocol with a builtin version. This protocol is used to verify `im4m` manifest files used by Apple Secure Boot.

8. `AppleKeyMap`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces Apple Key Map protocols with builtin versions.

9. `AppleRtcRam`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple RTC RAM protocol with a builtin version.

    _Note:_ Builtin version of Apple RTC RAM protocol may filter out I/O attempts to certain RTC memory addresses. The list of addresses can be specified in `4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102:rtc-blacklist` variable as a data array.

10. `AppleSecureBoot`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple Secure Boot protocol with a builtin version.

11. `AppleSmcIo`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple SMC I/O protocol with a builtin version.

    This protocol replaces the legacy `VirtualSmc` UEFI driver, and is compatible with any SMC kernel extension. However, in case the `FakeSMC` kernel extension is used, manual NVRAM key variable addition may be needed.


12. `AppleUserInterfaceTheme`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Apple User Interface Theme protocol with a builtin version.

13. `DataHub`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Data Hub protocol with a builtin version.

    _Note:_ This will discard all previous entries if the protocol was already installed, so all properties required for the safe operation of the system must be specified in the configuration file.

14. `DeviceProperties`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the Device Property protocol with a builtin version. This may be used to ensure full compatibility on VMs and legacy Macs.
    
    _Note:_ This will discard all previous entries if the protocol was already installed, so all properties required for safe operation of the system must be specified in the configuration file.

15. `FirmwareVolume`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Wraps Firmware Volume protocols, or installs a new version, to support custom cursor images for FileVault 2. Set to `true` to ensure FileVault 2 compatibility on anything other than on VMs and legacy Macs.

    _Note:_ Several virtual machines, including VMware, may have corrupted cursor images in HiDPI mode and thus, may also require enabling this setting.

16. `HashServices`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces Hash Services protocols with builtin versions. Set to `true` to ensure FileVault 2 compatibility on platforms with defective SHA-1 hash implementations. This can be determined by an invalid cursor size when `UIScale` is set to `02`. Platforms earlier than APTIO V (Haswell and older) are typically affected.

17. `OSInfo`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces the OS Info protocol with a builtin version. This protocol is typically used by the firmware and other applications to receive notifications from the macOS bootloader.

18. `UnicodeCollation`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Replaces unicode collation services with builtin versions. Set to `true` to ensure UEFI Shell compatibility on platforms with defective unicode collation implementations. Legacy Insyde and APTIO platforms on Ivy Bridge, and earlier, are typically affected.