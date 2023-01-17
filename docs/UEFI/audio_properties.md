---
layout: default
title: Audio Properties
nav_order: 13
parent: UEFI
---

# Audio Properties

1. `AudioCodec`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Codec address on the specified audio controller for audio support.
    
    This typically contains the first audio codec address on the builtin analog audio controller (`HDEF`). Audio codec addresses, e.g. `2`, can be found in the debug log (marked in bold-italic):
    
    ```
    OCAU: 1/3 PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x1)/VenMsg(<redacted>,00000000) (4 outputs)
    OCAU: 2/3 PciRoot(0x0)/Pci(0x3,0x0)/VenMsg(<redacted>,00000000) (1 outputs)
    OCAU: 3/3 PciRoot(0x0)/Pci(0x1B,0x0)/VenMsg(<redacted>,02000000) (7 outputs)
    ```
    As an alternative, this value can be obtained from `IOHDACodecDevice` class in I/O Registry containing it in `IOHDACodecAddress` field.

2. `AudioDevice`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Device path of the specified audio controller for audio support.
    This typically contains builtin analog audio controller (`HDEF`) device path, e.g. `PciRoot(0x0)/Pci(0x1b,0x0)`. The list of recognised audio controllers can be found in the debug log (marked in bold-italic):

    ```
    OCAU: 1/3 PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x1)/VenMsg(<redacted>,00000000) (4 outputs)
    OCAU: 2/3 PciRoot(0x0)/Pci(0x3,0x0)/VenMsg(<redacted>,00000000) (1 outputs)
    OCAU: 3/3 PciRoot(0x0)/Pci(0x1B,0x0)/VenMsg(<redacted>,02000000) (7 outputs)
    ```

    If using `AudioDxe`, the available controller device paths are also output on lines formatted like this:
    
    ```
    HDA: Connecting controller - PciRoot(0x0)/Pci(0x1B,0x0)
    ```

    Finally, `gfxutil -f HDEF` command can be used in macOS to obtain the device path.
    
    Specifying an empty device path results in the first available codec and audio controller being used. The value of `AudioCodec` is ignored in this case. This can be a convenient initial option to try to get UEFI audio working. Manual settings as above will be required when this default value does not work.

3. `AudioOutMask`
    - **Type:** `plist integer`
    - **Failsafe:** `-1`
    - **Description:** Bit field indicating which output channels to use for UEFI sound.

    Audio mask is 1 `«` audio output (equivalently `2 ˆ` audio output). E.g. for audio output `0` the bitmask is `1`, for output `3` it is `8`, and for outputs `0` and `3` it is `9`
    .
    The number of available output nodes (`N`) for each HDA codec is shown in the debug log (marked in bold-italic), audio outputs `0` to `N - 1` may be selected:

    ```
    OCAU: 1/3 PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x1)/VenMsg(<redacted>,00000000) (4 outputs)
    OCAU: 2/3 PciRoot(0x0)/Pci(0x3,0x0)/VenMsg(<redacted>,00000000) (1 outputs)
    OCAU: 3/3 PciRoot(0x0)/Pci(0x1B,0x0)/VenMsg(<redacted>,02000000) (7 outputs)
    ```

    When `AudioDxe` is used then additional information about each output channel is logged during driver binding, including the bitmask for each output. The bitmask values for the desired outputs should be added together to obtain the `AudioOutMask` value:

    ```
    HDA: | Port widget @ 0x9 is an output (pin defaults 0x2B4020) (bitmask 1)
    HDA: | Port widget @ 0xA is an output (pin defaults 0x90100112) (bitmask 2)
    HDA: | Port widget @ 0xB is an output (pin defaults 0x90100110) (bitmask 4)
    HDA: | Port widget @ 0x10 is an output (pin defaults 0x4BE030) (bitmask 8)
    ```
    
    Further information on the available output channels may be found from a Linux codec dump using the command: 
    
    `cat /proc/asound/card{n}/codec#{m}`
    
    Using `AudioOutMask`, it is possible to play sound to more than one channel (e.g. main speaker plus bass speaker; headphones plus speakers) as long as all the chosen outputs support the sound file format in use; if any do not then no sound will play and a warning will be logged.

    When all available output channels on the codec support the available sound file format then a value of `-1` will play sound to all channels simultaneously. If this does not work it will usually be quickest to try each available output channel one by one, by setting `AudioOutMask` to `1`, `2`, `4`, etc., up to `2 ˆ N - 1`, in order to work out which channel(s) produce sound.

4. `AudioSupport`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Activate audio support by connecting to a backend driver.

    Enabling this setting routes audio playback from builtin protocols to specified (`AudioOutMask`) dedicated audio ports of the specified codec (`AudioCodec`), located on the specified audio controller (`AudioDevice`).

5. `DisconnectHda`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Disconnect HDA controller before loading drivers.

    May be required on some systems (e.g. Apple hardware, VMware Fusion guest) to allow a UEFI sound driver (such as `AudioDxe`) to take control of the audio hardware.
    
    _Note:_ In addition to this option, most Apple hardware also requires the `--gpio-setup` driver argument which is dealt with in the [`AudioDxe`]({%link docs/UEFI/audiodxe.md%}) section.

6. `MaximumGain`
    - **Type:** `plist integer`
    - **Failsafe:** `-15`
    - **Description:** Maximum gain to use for UEFI audio, specified in decibels (dB) with respect to amplifier reference level of 0 dB (see note 1).
    
    All UEFI audio will use this gain setting when the system amplifier gain read from the `SystemAudioVolumeDB` NVRAM variable is higher than this. This is to avoid over-loud UEFI audio when the system volume is set very high, or the `SystemAudioVolumeDB` NVRAM value has been misconfigured.
    
    _Note 1:_ Decibels (dB) specify gain (postive values; increase in volume) or attenuation (negative values; decrease in volume) compared to some reference level. When you hear the sound level of a jet plane expressed as 120 decibels, say, the reference level is the sound level just audible to an average human. However generally in acoustic science and computer audio any reference level can be specified. Intel HDA and macOS natively use decibels to specify volume level. On most Intel HDA hardware the reference level of 0 dB is the _loudest_ volume of the hardware, and all lower volumes are therefore negative numbers. The quietest volume on typical sound hardware is around -55 dB to -60 dB.
    
    _Note 2:_ Matching how macOS handles decibel values, this value is converted to a signed byte; therefore values outside −128 dB to +127 dB (which are well beyond physically plausible volume levels) are not allowed.
    
    _Note 3:_ Digital audio output – which does not have a volume slider in-OS – ignores this and all other gain settings, only mute settings are relevant.

7. `MinimumAssistGain`
    - **Type:** `plist integer`
    - **Failsafe:** `-30`
    - **Description:** Minimum gain in decibels (dB) to use for picker audio assist.
    
    The screen reader will use this amplifier gain if the system amplifier gain read from the `SystemAudioVolumeDB` NVRAM variable is lower than this.
    
    _Note 1:_ In addition to this setting, because audio assist must be audible to serve its function, audio assist is not muted even if the OS sound is muted or the `StartupMute` NVRAM variable is set.
   
    _Note 2:_ See `MaximumGain` for an explanation of decibel volume levels.

8. `MinimumAudibleGain`
    - **Type:** `plist integer`
    - **Failsafe:** `-128`
    - **Description:** Minimum gain in decibels (dB) at which to attempt to play any sound.

    The boot chime will not play if the system amplifier gain level in the `SystemAudioVolumeDB` NVRAM variable is lower than this.
    
    _Note 1:_ This setting is designed to save unecessary pauses due to audio setup at inaudible volume levels, when no sound will be heard anyway. Whether there are inaudible volume levels depends on the hardware. On some hardware (including Apple) the audio values are well enough matched to the hardware that the lowest volume levels available are very quiet but audible, whereas on some other hardware combinations, the lowest part of the volume range may not be audible at all.
    
    _Note 2:_ See `MaximumGain` for an explanation of decibel volume levels.

9. `PlayChime`
    - **Type:** `plist string`
    - **Failsafe:** `Auto`
    - **Description:** Play chime sound at startup.

    Enabling this setting plays the boot chime using the builtin audio support. The volume level is determined by the `SystemAudioVolumeDB` NVRAM variable. Supported values are:
    
    - `Auto` — Enables chime when `StartupMute` NVRAM variable is not present or set to `00`.
    - `Enabled` — Enables chime unconditionally.
    - `Disabled` — Disables chime unconditionally.
    
    _Note 1:_ Enabled can be used separately from the StartupMute NVRAM variable to avoid conflicts when the firmware is able to play the boot chime.
    
    _Note 2:_ Regardless of this setting, the boot chime will not play if system audio is muted, i.e. if the `SystemAudioVolume` NVRAM variable has bit 0x80 set.

10. `ResetTrafficClass`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set HDA Traffic Class Select Register to `TC0`.

    AppleHDA kext will function correctly only if `TCSEL` register is configured to use `TC0` traffic class. Refer to Intel I/O Controller Hub 9 (ICH9) Family Datasheet (or any other ICH datasheet) for more details about this register.

    Note: This option is independent from `AudioSupport`. If AppleALC is used it is preferred to use AppleALC `alctcsel` property instead.

11. `SetupDelay`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Audio codec reconfiguration delay in milliseconds.
    
    Some codecs require a vendor-specific delay after the reconfiguration (e.g. volume setting). This option makes it configurable. A typical delay can be up to 0.5 seconds.