---
layout: default
title: Output Properties
nav_order: 16
parent: UEFI
---

# Output Properties

1. `TextRenderer`
    - **Type:** `plist string`
    - **Failsafe:** `BuiltinGraphics`
    - **Description:** Chooses renderer for text going through standard console output.

    Currently two renderers are supported: `Builtin` and `System`. `System` renderer uses firmware services for text rendering. `Builtin` bypassing firmware services and performs text rendering on its own. Different renderers support a different set of options. It is recommended to use `Builtin` renderer, as it supports HiDPI mode and uses full screen resolution.
    UEFI firmware typically supports ConsoleControl with two rendering modes: Graphics and Text. Some types of firmware do not support ConsoleControl and rendering modes. OpenCore and macOS expect text to only be shown in Graphics mode and graphics to be drawn in any mode. Since this is not required by UEFI specification, exact behaviour varies.

    Valid values are combinations of text renderer and rendering mode:
    - `BuiltinGraphics` — Switch to `Graphics` mode and use `Builtin` renderer with custom `ConsoleControl`.
    - `BuiltinText` — Switch to `Text` mode and use `Builtin` renderer with custom `ConsoleControl`.
    - `SystemGraphics` — Switch to `Graphics` mode and use `System` renderer with custom `ConsoleControl`.
    - `SystemText` — Switch to `Text` mode and use `System` renderer with custom `ConsoleControl`.
    - `SystemGeneric` — Use `System` renderer with system `ConsoleControl` assuming it behaves correctly.
    
    The use of `BuiltinGraphics` is straightforward. For most platforms, it is necessary to enable `ProvideConsoleGop` and set `Resolution` to `Max`. The `BuiltinText` variant is an alternative `BuiltinGraphics` for some very old and defective laptop firmware, which can only draw in `Text` mode.

    The use of `System` protocols is more complicated. Typically, the preferred setting is `SystemGraphics` or `SystemText`. Enabling `ProvideConsoleGop`, setting `Resolution` to `Max`, enabling `ReplaceTabWithSpace` is useful on almost all platforms. `SanitiseClearScreen`, `IgnoreTextInGraphics`, and `ClearScreenOnModeSwitch` are more specific, and their use depends on the firmware.

    _Note:_ Some Macs, such as the `MacPro5,1`, may have incompatible console output when using modern GPUs, and thus only `BuiltinGraphics` may work for them in such cases. NVIDIA GPUs may require additional [firmware upgrades](https://github.com/acidanthera/bugtracker/issues/1280).

2. `ConsoleMode`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Maintain current console mode)
    - **Description:** Sets console output mode as specified with the `WxH` (e.g. `80x24`) formatted string.

    Set to `Max` to attempt using the largest available console mode. This option is currently ignored as the `Builtin` text renderer only supports one console mode.

    _Note:_ This field is best left empty on most types of firmware.

3. `Resolution`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Maintain current screen resolution)
    - **Description:** Sets console output screen resolution.

    - Set to `WxH@Bpp` (e.g. `1920x1080@32`) or `WxH` (e.g.` 1920x1080`) formatted string to request custom resolution from GOP if available.
    - Set to `Max` to attempt using the largest available screen resolution.

    On HiDPI screens `APPLE_VENDOR_VARIABLE_GUID` `UIScale` NVRAM variable may need to be set to `02` to enable HiDPI scaling in `Builtin` text renderer, FileVault 2 UEFI password interface, and boot screen logo. Refer to the [Recommended Variables]({%link docs/NVRAM/recommended_variables.md %}) section for details.

    _Note:_ This will fail when console handle has no GOP protocol. When the firmware does not provide it, it can be added with `ProvideConsoleGop` set to `true`.

4. `ForceResolution`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Forces `Resolution` to be set in cases where the desired resolution is not available by default, such as on legacy Intel GMA and first generation Intel HD Graphics (Ironlake/Arrandale). Setting `Resolution` to `Max` will try to pull the largest available resolution from the connected display’s EDID.

    _Note:_ This option depends on the [`OC_FORCE_RESOLUTION_PROTOCOL`](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Protocol/OcForceResolution.h) protocol being present. This protocol is currently only supported by `OpenDuetPkg`. The `OpenDuetPkg` implementation currently only supports Intel iGPUs.

5. `ClearScreenOnModeSwitch`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Some types of firmware only clear part of the screen when switching from graphics to text mode, leaving a fragment of previously drawn images visible. This option fills the entire graphics screen with black colour before switching to text mode.

    _Note:_ This option only applies to `System` renderer.

6. `DirectGopRendering`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Use builtin graphics output protocol renderer for console.

    On certain firmware, such as on the `MacPro5,1`, this may provide better performance or fix rendering issues. However, this option is not recommended unless there is an obvious benefit as it may result in issues such as slower scrolling.

    This renderer fully supports `AppleEg2Info` protocol and will provide screen rotation for all EFI applications. In order to provide seamless rotation compatibility with `EfiBoot`, builtin `AppleFramebufferInfo` should also be used, i.e. it may need to be overridden on Mac EFI.

7. `GopPassThrough`
    - **Type:** `plist string`
    - **Failsafe:** `Disabled`
    - **Description:** Provide GOP protocol instances on top of UGA protocol instances.

    This option provides the GOP protocol via a UGA-based proxy for firmware that do not implement the protocol. The supported values for the option are as follows:

    - `Enabled` — provide GOP for all UGA protocols.
    - `Apple` — provide GOP for `AppleFramebufferInfo`-enabled protocols.
    - `Disabled` — do not provide GOP.

    _Note:_ This option requires ProvideConsoleGop to be enabled.

8. `IgnoreTextInGraphics`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Some types of firmware output text onscreen in both graphics and text mode. This is typically unexpected as random text may appear over graphical images and cause UI corruption. Setting this option to true will discard all text output when console control is in a different mode from `Text`.

    _Note:_ This option only applies to the `System` renderer.

9. `ReplaceTabWithSpace`
    - **Type:** `plist boolean`
    - **Failsafe:** false
    - **Description:** Some types of firmware do not print tab characters or everything that follows them, causing difficulties in using the UEFI Shell’s builtin text editor to edit property lists and other documents. This option makes the console output spaces instead of tabs.

    _Note:_ This option only applies to `System` renderer.

10. `ProvideConsoleGop`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Ensure GOP (Graphics Output Protocol) on console handle.
    macOS bootloader requires GOP or UGA (for 10.4 EfiBoot) to be present on console handle, yet the exact location of the graphics protocol is not covered by the UEFI specification. This option will ensure GOP and UGA, if present, are available on the console handle.

    _Note:_ This option will also replace incompatible implementations of GOP on the console handle, as may be the case on the `MacPro5,1` when using modern GPUs.

11. `ReconnectGraphicsOnConnect`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Reconnect all graphics drivers during driver connection.

    On certain firmware, it may be desireable to use an alternative graphics driver, for example BiosVideo.efi, providing better screen resolution options on legacy machines, or a driver supporting `ForceResolution`. This option attempts to disconnect all currently connected graphics drivers before connecting newly loaded drivers.

    _Note:_ This option requires `ConnectDrivers` to be enabled.

12. `ReconnectOnResChange`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Reconnect console controllers after changing screen resolution.

    On certain firmware, the controllers that produce the console protocols (simple text out) must be reconnected when the screen resolution is changed via GOP. Otherwise, they will not produce text based on the new resolution.

    _Note:_ On several boards this logic may result in black screen when launching OpenCore from Shell and thus it is optional. In versions prior to 0.5.2 this option was mandatory and not configurable. Please do not use this unless required.

13. `SanitiseClearScreen`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Some types of firmware reset screen resolutions to a failsafe value (such as `1024x768`) on the attempts to clear screen contents when large display (e.g. 2K or 4K) is used. This option attempts to apply a workaround.

    _Note:_ This option only applies to the `System` renderer. On all known affected systems, `ConsoleMode` must be set to an empty string for this option to work.

14. `UIScale`
    - **Type:** `plist integer`, 8 bit
    - **Failsafe:** `-1`
    - **Description:** User interface scaling factor.

    Corresponds to `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:UIScale` variable.

    - `1` — 1x scaling, corresponds to normal displays.
    - `2` — 2x scaling, corresponds to HiDPI displays.
    - `-1` — leaves the current variable unchanged.
    - `0` — automatically chooses scaling based on the current resolution.

    _Note 1:_ Automatic scale factor detection works on the basis of total pixel area and may fail on small HiDPI displays, in which case the value may be manually managed using the NVRAM section.

    _Note 2:_ When switching from manually specified NVRAM variable to this preference an NVRAM reset may be needed.

15. `UgaPassThrough`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Provide UGA protocol instances on top of GOP protocol instances.
    
    Some types of firmware do not implement the legacy UGA protocol but this may be required for screen output by older EFI applications such as EfiBoot from 10.4.