---
layout: default
title: Boot
parent: Misc
has_toc: true
nav_order: 3
---

# Boot

1. `ConsoleAttributes`
    **Type:** `plist integer`
    **Failsafe:** `0`
    **Description:** Sets specific attributes for the console.
    The text renderer supports colour arguments as a sum of foreground and background colours based on the UEFI specification. The value for black background and for black foreground, `0`, is reserved.

    List of colour values and names:

    - `0x00` — `EFI_BLACK`
    - `0x01` — `EFI_BLUE`
    - `0x02` — `EFI_GREEN`
    - `0x03` — `EFI_CYAN`
    - `0x04` — `EFI_RED`
    - `0x05` — `EFI_MAGENTA`
    - `0x06` — `EFI_BROWN`
    - `0x07` — `EFI_LIGHTGRAY`
    - `0x08` — `EFI_DARKGRAY`
    - `0x09` — `EFI_LIGHTBLUE`
    - `0x0A` — `EFI_LIGHTGREEN`
    - `0x0B` — `EFI_LIGHTCYAN`
    - `0x0C` — `EFI_LIGHTRED`
    - `0x0D` — `EFI_LIGHTMAGENTA`
    - `0x0E` — `EFI_YELLOW`
    - `0x0F` — `EFI_WHITE`
    - `0x00` — `EFI_BACKGROUND_BLACK`
    - `0x10` — `EFI_BACKGROUND_BLUE`
    - `0x20` — `EFI_BACKGROUND_GREEN`
    - `0x30` — `EFI_BACKGROUND_CYAN`
    - `0x40` — `EFI_BACKGROUND_RED`
    - `0x50` — `EFI_BACKGROUND_MAGENTA`
    - `0x60` — `EFI_BACKGROUND_BROWN`
    - `0x70` — `EFI_BACKGROUND_LIGHTGRAY`

    _Note:_ This option may not work well with the `System` text renderer. Setting a background different from black could help with testing GOP functionality.

2. `HibernateMode`
    **Type:** `plist string`
    **Failsafe:** `None`
    **Description:** Hibernation detection mode. The following modes are supported:

    - `None` — Ignore hibernation state.
    - `Auto` — Use RTC and NVRAM detection.
    - `RTC` — Use RTC detection.
    - `NVRAM` — Use NVRAM detection.
    Note: If the firmware can handle hibernation itself (valid for Mac EFI firmware), then `None` should be specified to hand-off hibernation state as is to OpenCore.

3. `HibernateSkipsPicker`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Do not show picker if waking from macOS hibernation.

    Limitations:
    - Only supports macOS hibernation wake, Windows and Linux are currently out of scope.
    - Should only be used on systems with reliable hibernation wake in macOS, otherwise users may not be able to visually see boot loops that may occur.
    - Highly recommended to pair this option with `PollAppleHotKeys`, allows to enter picker in case of issues with hibernation wake.
    - Visual indication for hibernation wake is currently out of scope.

4. `HideAuxiliary`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Set to `true` to hide auxiliary entries from the picker menu.

    An entry is considered auxiliary when at least one of the following applies:
    - Entry is macOS recovery.
    - Entry is macOS Time Machine.
    - Entry is explicitly marked as `Auxiliary`.
    - Entry is system (e.g. `Reset NVRAM`).

    To display all entries, the picker menu can be reloaded into “Extended Mode” by pressing the `Spacebar` key. Hiding auxiliary entries may increase boot performance on multi-disk systems.

5. `LauncherOption`
    **Type:** `plist string`
    **Failsafe:** `Disabled`
    **Description:** Register the launcher option in the firmware preferences for persistence.

    Valid values:
    - `Disabled` — do nothing.
    - `Full` — create or update the top priority boot option in UEFI variable storage at bootloader startup.
        – For this option to work, `RequestBootVarRouting` is required to be enabled.
    - `Short` — create a short boot option instead of a complete one.
        – This variant is useful for some older types of firmware, typically from Insyde, that are unable to manage full device paths.
    - `System` — create no boot option but assume specified custom option is blessed.
        – This variant is useful when relying on `ForceBooterSignature` quirk and OpenCore launcher path
    management happens through bless utilities without involving OpenCore.

    This option allows integration with third-party operating system installation and upgrades (which may overwrite the `\EFI\BOOT\BOOTx64.efi` file). The BOOTx64.efi file is no longer used for bootstrapping OpenCore if a custom option is created. The custom path used for bootstrapping can be specified by using the `LauncherPath` option.
    
    _Note 1:_ Some types of firmware may have NVRAM implementation flaws, no boot option support, or other incompatibilities. While unlikely, the use of this option may result in boot failures and should only be used exclusively on boards known to be compatible. Refer to [acidanthera/bugtracker#1222](https://github.com/acidanthera/bugtracker/issues/1222) for some known issues affecting Haswell and other boards.
    _Note 2:_ While NVRAM resets executed from OpenCore would not typically erase the boot option created in `Bootstrap`, executing NVRAM resets prior to loading OpenCore will erase the boot option. Therefore, for significant implementation updates, such as was the case with OpenCore 0.6.4, an NVRAM reset should be executed with `Bootstrap` disabled, after which it can be re-enabled.
    _Note 3:_ Some versions of Intel Visual BIOS (e.g. on Intel NUC) have an unfortunate bug whereby if any boot option is added referring to a path on a USB drive, from then on that is the only boot option which will be shown when any USB drive is inserted. If OpenCore is started from a USB drive on this firmware with `LauncherOption` set to `Full` or `Short`, this applies and only the OpenCore boot entry will be seen afterwards, when any other USB is inserted (this highly non-standard BIOS behaviour affects other software as well). The best way to avoid this is to leave `LauncherOption` set to Disabled or System on any version of OpenCore which will be started from a USB drive on this firmware. If the problem has already occurred the quickest reliable fix is:

    - Enable the system UEFI Shell in Intel Visual BIOS - With power off, insert an OpenCore USB
    - Power up and select the system UEFI Shell
    - Since the system shell does not include `bcfg`, use the system shell to start OpenCore’s OpenShell (e.g. by entering the command `FS2:\EFI\OC\Tools\OpenShell.efi` , but you will need to work out which drive is correct for OpenCore and modify the drive number `FS#:` accordingly)
    - Within OpenShell, use `bcfg boot dump` to display the NVRAM boot options and then use `bcfg boot rm #` (where `#` is the number of the OpenCore boot entry) to remove the OpenCore entry
    
    It is alternatively possible to start OpenShell directly from the OpenCore boot menu, if you have a working configured OpenCore for the system. In that case, and if OpenCore has `RequestBootVarRouting` enabled, it will be necessary to run the command` \EFI\OC\Tools\OpenControl.efi disable` before using `bcfg`. (After `OpenControl disable`, it is necessary to either reboot or run `OpenControl restore`, before booting an operating system.) It is also possible to use `efibootmgr` within Linux to remove the offending entry, if you have a working version of Linux on the machine. Linux must be started either not via OpenCore, or via OpenCore with `RequestBootVarRouting` disabled for this to work.

6. `LauncherPath`
    **Type:** `plist string`
    **Failsafe:** `Default`
    **Description:** Launch path for the `LauncherOption` property.

    Default points to `OpenCore.efi`. User specified paths, e.g. `\EFI\SomeLauncher.efi`, can be used to provide custom loaders, which are supposed to load `OpenCore.efi` themselves.

7. `PickerAttributes`
    **Type:** `plist integer`
    **Failsafe:** `0`
    **Description:** Sets specific attributes for the OpenCore picker.
    
    Different OpenCore pickers may be configured through the attribute mask containing OpenCore-reserved (`BIT0~BIT15`) and OEM-specific (`BIT16~BIT31`) values.

    Current OpenCore values include:
    
    - `0x0001` — `OC_ATTR_USE_VOLUME_ICON`, provides custom icons for boot entries:
        OpenCore will attempt loading a volume icon by searching as follows, and will fallback to the default icon on failure:
        – `.VolumeIcon.icns` file at `Preboot` volume in per-volume directory (`/System/Volumes/Preboot/{GUID}/` when mounted at the default location within macOS) for APFS (if present).
        – `.VolumeIcon.icns` file at the `Preboot` volume root (`/System/Volumes/Preboot/`, when mounted at the default location within macOS) for APFS (otherwise).
        – `.VolumeIcon.icns` file at the volume root for other filesystems.

    _Note 1:_ The Apple picker partially supports placing a volume icon file at the operating system’s `Data` volume root, `/System/Volumes/Data/`, when mounted at the default location within macOS. This approach is flawed: the file is neither accessible to OpenCanopy nor to the Apple picker when FileVault 2, which is meant to be the default choice, is enabled. Therefore, OpenCanopy does not attempt supporting Apple’s approach. A volume icon file may be placed at the root of the `Preboot` volume for compatibility with both OpenCanopy and the Apple picker, or use the `Preboot` per-volume location as above with OpenCanopy as a preferred alternative to Apple’s approach.
    _Note 2:_ Be aware that using a volume icon on any drive overrides the normal OpenCore picker behaviour for that drive of selecting the appropriate icon depending on whether the drive is internal or external.
    
    
    - `0x0002` — `OC_ATTR_USE_DISK_LABEL_FILE`, use custom prerendered titles for boot entries from `.disk_label` (`.disk_label_2x`) file next to the bootloader for all filesystems. These labels can be generated via the `disklabel` utility or the `bless --folder {FOLDER_PATH} --label {LABEL_TEXT}` command. When pre-rendered labels are disabled or missing, use label text in `.contentDetails` (or `.disk_label.contentDetails`) file next to bootloader if present instead, otherwise the entry name itself will be rendered.

    - `0x0004` — `OC_ATTR_USE_GENERIC_LABEL_IMAGE`, provides predefined label images for boot entries without custom entries. This may however give less detail for the actual boot entry.

    - `0x0008` — `OC_ATTR_HIDE_THEMED_ICONS`, prefers builtin icons for certain icon categories to match the theme style. For example, this could force displaying the builtin Time Machine icon. Requires `OC_ATTR_USE_VOLUME_ICON`.
    - `0x0010` — `OC_ATTR_USE_POINTER_CONTROL`, enables pointer control in the OpenCore picker when available. For example, this could make use of mouse or trackpad to control UI elements.
    - `0x0020` — `OC_ATTR_SHOW_DEBUG_DISPLAY`, enable display of additional timing and debug information, in Builtin picker in `DEBUG` and `NOOPT` builds only.
    - `0x0040` — `OC_ATTR_USE_MINIMAL_UI`, use minimal UI display, no Shutdown or Restart buttons, affects OpenCanopy and builtin picker.
    - `0x0080` — `OC_ATTR_USE_FLAVOUR_ICON`, provides flexible boot entry content description, suitable for picking the best media across different content sets:
    
    When enabled, the entry icon in OpenCanopy and the audio assist entry sound in OpenCanopy and builtin boot picker are chosen by something called content flavour. To determine content flavour the following algorithm is used:
    – For a Tool the value is read from `Flavour` field.
    – For an automatically discovered entry, including for boot entry protocol entries such as those generated by the OpenLinuxBoot driver, it is read from the `.contentFlavour` file next to the bootloader, if present.
    – For a custom entry specified in the `Entries` section it is read from the `.contentFlavour` file next to the bootloader if `Flavour` is `Auto`, otherwise it is specified via the `Flavour` value itself.
    – If read flavour is `Auto` or there is no `.contentFlavour`, entry flavour is chosen based on the entry type (e.g. Windows automatically gets Windows flavour).
    
    The `Flavour` value is a sequence of `:` separated names limited to 64 characters of printable 7-bit ASCII. This is designed to support up to approximately five names. Each name refers to a flavour, with the first name having the highest priority and the last name having the lowest priority. Such a structure allows describing an entry in a more specific way, with icons selected flexibly depending on support by the audio-visual pack. A missing audio or icon file means the next flavour should be tried, and if all are missing the choice happens based on the type of the entry. Example flavour values:` BigSur:Apple`, `Windows10:Windows`. `OpenShell:UEFIShell:Shell`.
    
    Using flavours means that you can switch between icon sets easily, with the flavour selecting the best available icons from each set. E.g. specifying icon flavour `Debian:Linux` will use the icon `Debian.icns` if provided, then will try `Linux.icns`, then will fall back to the default for an OS, which is `HardDrive.icns`.
    
    Things to keep in mind:
    – For security reasons `Ext<Flavour>.icns` and `<Flavour>.icns` are both supported, and only `Ext<Flavour>.icns` will be used if the entry is on an external drive (followed by default fallback `ExtHardDrive.icns`).
    – Where both apply `.VolumeIcon.icns` takes precence over `.contentFlavour`.
    – In order to allow icons and audio assist to work correctly for tools (e.g. for UEFI Shell), system default boot entry icons (see `Docs/Flavours.md`) specified in the `Flavour` setting for `Tools` or `Entries` will continue to apply even when flavour is disabled. Non-system icons will be ignored in this case. In addition, the flavours `UEFIShell` and `NVRAMReset` are given special processing, identifying their respective tools to apply correct audio-assist, default builtin labels, etc.
    – A list of recommended flavours is provided in `Docs/Flavours.md`.

8. `PickerAudioAssist`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Enable screen reader by default in the OpenCore picker.
    For the macOS bootloader, screen reader preference is set in the `preferences.efires` archive in the `isVOEnabled.int32` file and is controlled by the operating system. For OpenCore screen reader support, this option is an independent equivalent. Toggling screen reader support in both the OpenCore picker and the macOS bootloader FileVault 2 login window can also be done by using the `Command + F5` key combination.
    
    _Note:_ The screen reader requires working audio support. Refer to the [UEFI Audio Properties]({%link docs/UEFI/audio_properties.md %}) section for details.

9. `PollAppleHotKeys`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Enable modifier hotkey handling in the OpenCore picker.
    In addition to `action hotkeys`, which are partially described in the `PickerMode` section and are typically handled by Apple BDS, modifier keys handled by the operating system bootloader (`boot.efi`) also exist. These keys allow changing the behaviour of the operating system by providing different boot modes.

    On certain firmware, using modifier keys may be problematic due to driver incompatibilities. To workaround this problem, this option allows registering certain hotkeys in a more permissive manner from within the OpenCore picker. Such extensions include support for tapping on key combinations before selecting the boot item, and for reliable detection of the `Shift` key when selecting the boot item, in order to work around the fact that hotkeys which are continuously held during boot cannot be reliably detected on many PS/2 keyboards.

    This list of known modifier hotkeys includes:
    - `CMD+C+MINUS` — disable board compatibility checking.
    - `CMD+K` — boot release kernel, similar to `kcsuffix=release`.
    - `CMD+S` — single user mode.
    - `CMD+S+MINUS` — disable KASLR slide, requires disabled SIP.
    - `CMD+V` — verbose mode.
    - `Shift+Enter`, `Shift+Index` — safe mode, may be used in combination with `CTRL+Enter`, `CTRL+Index`.

10. `ShowPicker`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Show a simple picker to allow boot entry selection.

11. `TakeoffDelay`
    **Type:** `plist integer`, 32 bit
    **Failsafe:** `0`
    **Description:** Delay in microseconds executed before handling the OpenCore picker startup and action hotkeys.
    Introducing a delay may give extra time to hold the right `action hotkey` sequence to, for instance, boot into recovery mode. On most systems, the appearance of the initial boot logo is a good indication of the time from which hotkeys can be held down. Earlier than this, the key press may not be registered. On some platforms, setting this option to a minimum of `5000-10000` microseconds is also required to access action hotkeys due to the nature of the keyboard driver.

    If the boot chime is configured (see audio configuration options) then at the expense of slower startup, an even longer delay of half to one second (`500000-1000000`) may be used to create behaviour similar to a real Mac, where the chime itself can be used as a signal for when hotkeys can be pressed. The boot chime is inevitably later in the boot sequence in OpenCore than on Apple hardware, due to the fact that non-native drivers have to be

    loaded and connected first. Configuring the boot chime and adding this longer additional delay can also be useful in systems where fast boot time and/or slow monitor signal synchronisation may cause the boot logo not to be shown at all on some boots or reboots.

12. `Timeout`
    **Type:** `plist integer`, 32 bit
    **Failsafe:** `0`
    **Description:** Timeout in seconds in the OpenCore picker before automatic booting of the default boot entry. Set to `0` to disable.

13. `PickerMode`
    **Type:** `plist string`
    **Failsafe:** Builtin
    **Description:** Choose picker used for boot management.

    `PickerMode` describes the underlying boot management with an optional user interface responsible for handling boot options.
    
    The following values are supported:
    - `Builtin` — boot management is handled by OpenCore, a simple text-only user interface is used.
    - `External` — an external boot management protocol is used if available. Otherwise, the `Builtin` mode is used.
    - `Apple` — Apple boot management is used if available. Otherwise, the `Builtin` mode is used.

    Upon success, the `External` mode may entirely disable all boot management in OpenCore except for policy enforcement. In the `Apple` mode, it may additionally bypass policy enforcement. Refer to the [OpenCanopy]({%link docs/UEFI/opencanopy.md %}) plugin for an example of a custom user interface.

    The OpenCore built-in picker contains a set of actions chosen during the boot process. The list of supported actions is similar to Apple BDS and typically can be accessed by holding `action hotkeys` during the boot process.
    
    The following actions are currently considered:
    - `Default` — this is the default option, and it lets the built-in OpenCore picker load the default boot option as specified in the [Startup Disk](https://support.apple.com/HT202796) preference pane.
    - `ShowPicker` — this option forces the OpenCore picker to be displayed. This can typically be achieved by holding the `OPT` key during boot. Setting `ShowPicker` to `true` will make `ShowPicker` the default option.
    - `BootApple` — this options performs booting to the first Apple operating system found unless the chosen default operating system is one from Apple. Hold the `X` key down to choose this option.
    - `BootAppleRecovery` — this option performs booting into the Apple operating system recovery partition. This is either that related to the default chosen operating system, or first one found when the chosen default operating system is not from Apple or does not have a recovery partition. Hold the `CMD+R` hotkey combination down to choose this option.
    
    _Note 1:_ On non-Apple firmware `KeySupport`, `OpenUsbKbDxe`, or similar drivers are required for key handling. However, not all of the key handling functions can be implemented on several types of firmware.
    _Note 2:_ In addition to `OPT`, OpenCore supports using both the `Escape` and `Zero` keys to enter the OpenCore picker when `ShowPicker` is disabled. `Escape` exists to support co-existence with the Apple picker (including OpenCore `Apple` picker mode) and to support firmware that fails to report held `OPT` key, as on some PS/2 keyboards. In addition, `Zero` is provided to support systems on which `Escape` is already assigned to some other pre-boot firmware feature. In systems which do not require `KeySupport`, pressing and holding one of these keys from after power on until the picker appears should always be successful. The same should apply when using `KeySupport` mode if it is correctly configured for the system, i.e. with a long enough `KeyForgetThreshold`. If pressing and holding the key is not successful to reliably enter the picker, multiple repeated keypresses may be tried instead.
    
    _Note 3:_ On Macs with problematic GOP, it may be difficult to re-bless OpenCore if its bless status is lost. The `BootKicker` utility can be used to work around this problem, if set up as a Tool in OpenCore (e.g. on a CDROM) with `FullNvramAccess` enabled. It will launch the Apple picker, which allows selection of an item to boot next (with `Enter`), or next and subsequently, i.e. as the blessed entry (with `CTRL+Enter`), as normal. After the selection is made, the system will reboot and the chosen entry will be booted.

14. `PickerVariant`
    **Type:** `plist string`
    **Failsafe:** `Auto`
    **Description:** Choose specific icon set to be used for boot management.

    An icon set is a directory path relative to `Resources\Image`, where the icons and an optional manifest are located. It is recommended for the artists to use provide their sets in the `Vendor\Set` format, e.g. `Acidanthera\GoldenGate`.

    Sample resources provided as a part of [OcBinaryData repository](https://github.com/acidanthera/OcBinaryData) provide the following icon set:

    - `Acidanthera\GoldenGate` — macOS 11 styled icon set.
    - `Acidanthera\Syrah` — macOS 10.10 styled icon set.
    - `Acidanthera\Chardonnay` — macOS 10.4 styled icon set.
    
    For convenience purposes there also are predefined aliases:
    - `Auto` — Automatically select one set of icons based on the `DefaultBackground` colour: `Acidanthera\GoldenGate` for Syrah Black and `Acidanthera\Chardonnay` for Light Gray.
    - `Default` — `Acidanthera\GoldenGate`.
