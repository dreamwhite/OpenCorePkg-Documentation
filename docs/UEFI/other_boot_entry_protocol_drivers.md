---
layout: default
title: Other Boot Entry Protocol Drivers
nav_order: 7
parent: UEFI
---

# Other Boot Entry Protocol Drivers

In addition to the [OpenLinuxBoot]({%link docs/UEFI/openlinuxboot.md %}) plugin, the following `OC_BOOT_ENTRY_PROTOCOL` plugins are made available to add optional, configurable boot entries to the OpenCore boot picker.

## `ResetNvramEntry`

Adds a menu entry which resets NVRAM and immediately restarts. Additionally adds support for hotkey `CMD+OPT+P+R` to perform the same action. Note that on some combinations of firmware and drivers, the `TakeoffDelay` option must be configured in order for this and other builtin hotkeys to be reliably detected.

_Note 1:_ It is known that some Lenovo laptops have a firmware bug, which makes them unbootable after performing NVRAM reset. Refer to [acidanthera/bugtracker#995](https://github.com/acidanthera/bugtracker/issues/995) for details.

_Note 2:_ If `LauncherOption` is set to `Full` or `Short` then the OpenCore boot entry is protected. Resetting NVRAM will normally erase any other boot options not specified via `BlessOverride`, for example Linux installations to custom locations and not using the `OpenLinuxBoot` driver, or user-specified UEFI boot menu entries. To obtain reset NVRAM functionality which does not remove other boot options, it is possible to use the `--preserve-boot` option (though see the warning specified).

The following configuration options may be specified in the Arguments section for this driver:

- `--preserve-boot` - Boolean flag, enabled if present.

    If enabled, BIOS boot entries are not cleared during NVRAM reset. This option should be used with caution, as some boot problems can be fixed by clearing these entries.

- `--apple` - Boolean flag, enabled if present.

    On Apple firmware only, this performs a system NVRAM reset. This can result in additional, desirable operations such as NVRAM garbage collection. This is achieved by setting the `ResetNVRam` NVRAM variable. Where available, this has the same effect as pressing `CMD+OPT+P+R` during native boot, although note that if accessed from the menu entry only one boot chime will be heard.
    
    _Note 1:_ Due to using system NVRAM reset, this option is not compatible with the `--preserve-boot` option and will override it, therefore all BIOS boot entries will be removed.

    _Note 2:_ Due to using system NVRAM reset, the OpenCore boot option cannot be preserved and OpenCore will have to either be reselected in the native boot picker or re-blessed.
    
    _Note 3:_ On non-Apple hardware, this option will still set this variable but the variable will not be recognised by the firmware and no NVRAM reset will happen.

## `ToggleSipEntry`

Provides a boot entry for enabling and disabling System Integrity Protection (SIP) in OpenCore picker.

While macOS is running, SIP involves multiple configured software protection systems, however all the information about which of these protections to enable is stored in the single Apple NVRAM variable `csr-active-config`. As long as this variable is set before macOS startup, SIP will be fully configured, so setting the variable using this boot option (or in any other way, before macOS starts) has exactly the same end result as configuring SIP using the `csrutil` command in macOS Recovery.

`csr-active-config` will be toggled between `0` for enabled, and a user-specified or default value for disabled.
Options for the driver should be specified as plain text values separated by whitespace in the `Arguments` section of `Driver` entry. Available options are:

- `--show-csr` - Boolean flag, enabled if present.
    If enabled, show the current hexadecimal value of `csr-active-config` in the boot entry name. This option will not work in OpenCanopy when used in combination with `OC_ATTR_USE_GENERIC_LABEL_IMAGE` in `PickerAttributes`.

- Numerical value - Default value `0x27F`.
    Specify the `csr-active-config` value to use to disabled SIP. This can be specified as hexadecimal, beginning with `0x`, or as decimal. For more info see Note 2 below.

    _Note 1:_ It is recommended not to run macOS with SIP disabled. Use of this boot option may make it easier to quickly disable SIP protection when genuinely needed - it should be re-enabled again afterwards.
    
    _Note 2:_ The default value for disabling SIP with this boot entry is `0x27F`. For comparison, `csrutil disable` with no other arguments on macOS Big Sur and Monterey sets `0x7F`, and on Catalina it sets `0x77`. The OpenCore default value of `0x27F` is a variant of the Big Sur and Monterey value, chosen as follows:
    
    - `CSR_ALLOW_UNAPPROVED_KEXTS` (`0x200`) is included in the default value, since it is generally useful, in the case where you need to have SIP disabled anyway, to be able to install unsigned kexts without manual approval in System Preferences.
    - `CSR_ALLOW_UNAUTHENTICATED_ROOT` (`0x800`) is not included in the default value, as it is very easy when using it to inadvertently break OS seal and prevent incremental OTA updates.
    - If unsupported bits from a later OS are specified in `csr-active-config` (e.g. specifying `0x7F` on Catalina) then `csrutil status` will report that SIP has a non-standard value, however protection will be functionally the same.