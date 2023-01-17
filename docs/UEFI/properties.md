---
layout: default
title: Properties
nav_order: 10
parent: UEFI
---

# Properties

1. `APFS`
    - **Type:** `plist dict`
    - **Description:** Provide APFS support as configured in the [APFS Properties]({%link docs/UEFI/apfs_properties.md%}) section below.

2. `AppleInput`
    - **Type:** `plist dict`
    - **Description:** Configure the re-implementation of the Apple Event protocol described in the [AppleInput Properties]({%link docs/UEFI/appleinput_properties.md%}) section below.

3. `Audio`
    - **Type:** `plist dict`
    - **Description:** Configure audio backend support described in the [`Audio Properties`]({%link docs/UEFI/audio_properties.md%}) section below.
    
    Unless documented otherwise (e.g. `ResetTrafficClass`) settings in this section are for UEFI audio support only (e.g. OpenCore generated boot chime and audio assist) and are unrelated to any configuration needed for OS audio support (e.g. `AppleALC`).

    UEFI audio support provides a way for upstream protocols to interact with the selected audio hardware and resources. All audio resources should reside in `\EFI\OC\Resources\Audio` directory. Currently the supported audio file formats are MP3 and WAVE PCM. While it is driver-dependent which audio stream format is supported, most common audio cards support 16-bit signed stereo audio at 44100 or 48000 Hz.

    Audio file path is determined by audio type, audio localisation, and audio path. Each filename looks as follows: `[audio type]_[audio localisation]_[audio path].[audio ext]`. For unlocalised files filename does not include the language code and looks as follows: `[audio type]_[audio path].[audio ext]`. Audio extension can either be `mp3` or `wav`.
    
    - Audio type can be `OCEFIAudio` for OpenCore audio files or `AXEFIAudio` for macOS bootloader audio files.
    - Audio localisation is a two letter language code (e.g. `en`) with an exception for Chinese, Spanish, and Portuguese. Refer to [`APPLE_VOICE_OVER_LANGUAGE_CODE` definition](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/Protocol/AppleVoiceOver.h) for the list of all supported localisations.
    - Audio path is the base filename corresponding to a file identifier. For macOS bootloader audio paths refer to [`APPLE_VOICE_OVER_AUDIO_FILE` definition](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Apple/Protocol/AppleVoiceOver.h). For OpenCore audio paths refer to [`OC_VOICE_OVER_AUDIO_FILE` definition](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Protocol/OcAudio.h). The only exception is OpenCore boot chime file, which is `OCEFIAudio_VoiceOver_Boot.mp3`.
    
    Audio localisation is determined separately for macOS bootloader and OpenCore. For macOS bootloader it is set in `preferences.efires` archive in `systemLanguage.utf8` file and is controlled by the operating system. For OpenCore the value of `prev-lang:kbd` variable is used. When native audio localisation of a particular file is missing, English language (`en`) localisation is used. Sample audio files can be found in [OcBinaryData](https://github.com/acidanthera/OcBinaryData) repository.

4. `ConnectDrivers`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Perform UEFI controller connection after driver loading.

    This option is useful for loading drivers following UEFI driver model as they may not start by themselves. Examples of such drivers are filesystem or audio drivers. While effective, this option may not be necessary for drivers performing automatic connection, and may slightly slowdown the boot.
    
    _Note:_ Some types of firmware, particularly those made by Apple, only connect the boot drive to speed up the boot process. Enable this option to be able to see all the boot options when running multiple drives.

5. `Drivers`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Load selected drivers from OC/Drivers directory.
    
    To be filled with `plist dict` values, describing each driver. Refer to the [Drivers Properties]({%link docs/UEFI/drivers_properties.md %}) section below.

6. `Input`
    - **Type:** `plist dict`
    - **Description:** Apply individual settings designed for input (keyboard and mouse) in the [Input Properties]({%link docs/UEFI/input_properties.md %}) section below.

7. `Output`
    - **Type:** `plist dict`
    - **Description:** Apply individual settings designed for output (text and graphics) in the [Output Properties]({%link docs/UEFI/output_properties.md %}) section below.

8. `ProtocolOverrides`
    - **Type:** `plist dict`
    - **Description:** Force builtin versions of certain protocols described in the [ProtocolOverrides Properties]({%link docs/UEFI/protocoloverrides_properties.md %}) section below.
    
    _Note:_ all protocol instances are installed prior to driver loading.

9. `Quirks`
    - **Type:** `plist dict`
    - **Description:** Apply individual firmware quirks described in the [Quirks Properties]({%link docs/UEFI/quirks_properties.md %}) section below.

10. `ReservedMemory`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** To be filled with `plist dict` values, describing memory areas exclusive to specific firmware and hardware functioning, which should not be used by the operating system. Examples of such memory regions could be the second 256 MB corrupted by the Intel HD 3000 or an area with faulty RAM. Refer to the [ReservedMemory Properties]({%link docs/UEFI/reservedmemory_properties.md %}) section below for details.