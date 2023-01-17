---
layout: default
title: OpenVariableRuntimeDxe
nav_order: 9
parent: UEFI
---

# OpenVariableRuntimeDxe

Provides in-memory emulated NVRAM implementation. This can be useful on systems with fragile (e.g. MacPro5,1, see discussion linked from this [forum post](https://forums.macrumors.com/posts/30945127)) or incompatible NVRAM implementations. This driver is included by default in OpenDuet.

In addition to installing emulated NVRAM, this driver additionally installs an OpenCore compatible protocol enabling the following:

- NVRAM values are loaded from `NVRAM/nvram.plist` (or from `NVRAM/nvram.fallback` if it is present and `NVRAM/nvram.plist` is missing) on boot
- The Reset NVRAM option installed by the `ResetNvramEntry` driver removes the above files instead of affecting underlying NVRAM
- `CTRL+Enter` in the OpenCore bootpicker updates or creates `NVRAM/nvram.plist`

Recommended configuration settings for this driver:
- `OpenVariableRuntimeDxe.efi` loaded using `LoadEarly=true`. OpenDuet users should not load this driver, as it is included in OpenDuet.
- `OpenRuntime.efi` specified after `OpenVariableRuntimeDxe.efi` (when applicable), also loaded using `LoadEarly=true` for correct operation of `RequestBootVarRouting`.
    - `RequestBootVarRouting` is never strictly needed while using emulated NVRAM, but it can be convenient to leave it set on a system which needs to switch between real and emulated NVRAM.
    - `RequestBootVarRouting` is never required on an OpenDuet system, since there are no BIOS-managed boot entries to protect, therefore on OpenDuet recommended settings are `LoadEarly=false` for `OpenRuntime.efi` and `RequestBootVarRouting=false`.

- `LegacySchema` populated.
    - For simpler testing (allows arbitrary test variables), and future-proofing against changes in the variables required by macOS updates, use `<string>*</string>` settings, as described in notes below.
    - For increased security, populate sections with known required keys only, as shown in OpenCore’s sample `.plist` files.

- `ExposeSensitiveData` with at least bit `0x1` set to make boot-path variable containing the OpenCore EFI partition UUID available to the Launchd.command script.

Variable loading happens prior to the NVRAM `Delete` (and `Add`) phases. Unless `LegacyOverwrite` is enabled, it will not overwrite any existing variable. Variables allowed for loading and for saving with `CTRL+Enter` must be specified in `LegacySchema`.

In order to allow changes to NVRAM within macOS to be captured and saved, an additional script must be installed.
An example of such script can be found in [Utilities/LogoutHook/Launchd.command](https://github.com/acidanthera/OpenCorePkg/tree/master/Utilities/LogoutHook/Launchd.command).

_Note 1:_ This driver requires working FAT write support in firmware, and sufficient free space on the OpenCore EFI partition for up to three saved NVRAM files.
_Note 2:_ The `nvram.plist` (and `nvram.fallback` if present) files must have a root `plist dictionary` type and contain two fields:
    - `Version` — `plist integer`, file version, must be set to `1`.
    - `Add` — `plist dictionary`, equivalent to `Add` from `config.plist`.
_Note 3:_ When setting up legacy NVRAM, it can be convenient to set `<string>*</string>` as the value for the following three GUID keys in `LegacySchema`:

- `36C28AB5-6566-4C50-9EBD-CBB920F83843`
- `7C436110-AB2A-4BBB-A880-FE41995C9F82`
- `8BE4DF61-93CA-11D2-AA0D-00E098032B8C`

This enables all variables saved by `Launchd.command` to be saved to `nvram.plist`, therefore it allows all arbitrary user test variables (e.g. as set by `sudo nvram foo=bar`) to be saved. Using this permissive policy is also future-proof against any changes in the variables which need to be passed from macOS update setup to the `macOS Installer` stage, in order for it to succeed. Nevertheless, once emulated NVRAM is set up, only allowing known strictly required variables (as shown in OpenCore’s sample `.plist` files) is considerably more secure. See also the following warning about the overall security of loading NVRAM variables from a non-vaulted file.

**Warning:** The ability to load NVRAM from a file on disk can be dangerous, as it passes unprotected data to firmware variable services. Only use when no hardware NVRAM implementation is provided by the firmware or when the NVRAM implementation available in firmware is incompatible or dangerously fragile (e.g. in a state where excessive use may cause bricked hardware).

## Managing macOS updates when using emulated NVRAM

OpenCore combined with `OpenVariableRuntimeDxe` will only use a given saved `nvram.plist` file once, if it is used to launch a `macOS Installer` boot entry. After that the used settings are moved to `nvram.used` and fallback settings, if any, from `nvram.fallback` are used instead. `Launchd.command` itself always copies the previous NVRAM settings to fallback, each time it saves new settings.

This strategy is used to work round the limitation that the `Launchd.command` script is not running, and therefore cannot save NVRAM changes (particularly default boot entry changes), during the second and subsequent restarts of the macOS installer.

In brief, this fallback strategy allows full or incremental OTA updates of recent macOS, which are started from within an existing macOS (with the `Launchd.command` script installed), to proceed without manual intervention.

However, for full installs, there can be more than one full restart back to the `macOS Installer` entry. In this case the fallback strategy will lose track of the correct startup item (i.e. `macOS Installer`) from the second reboot onwards.
Equally, if installing to a drive other than the current default boot partition, this will not be automatically selected after the installer completes, as it would be when using non-emulated NVRAM. (This behaviour remains preferable to not having the fallback strategy, in which case a `macOS Installer` entry would be continually recreated in the picker menu, even once it no longer exists).

In both the above two cases it is recommended to use the following settings, to make it easy to manually control which boot entry is selected during the installer process:
- Set `ShowPicker=true`.
- Set `Timeout=0`.
- Set `DisableWatchdog=true`.
- If possible, start from a situation where there are no other pending `macOS Installer` entries in the boot menu (to avoid potential confusion as to which is relevant).

The first reboot should correctly select `macOS Installer`. For second and subsequent reboots, if a `macOS Installer` entry is still present it should be manually selected (using just `Enter`, not `CTRL+Enter`). Once a `macOS Installer` entry is no longer present, the entry for the new OS will still be automatically selected if it was the previous boot default. If not, it should be manually selected (at this point, `CTRL+Enter` is a good idea as any final remaining installion restarts will be to this entry).

_Note:_ When using emulated NVRAM but not installing from within an existing installed macOS (i.e. when installing from within macOS Recovery, or from an installation USB), please refer to this [forum post](https://applelife.ru/posts/916248) (in Russian) for additional options.