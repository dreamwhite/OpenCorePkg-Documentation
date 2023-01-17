---
layout: default
title: Introduction
parent: Misc
nav_order: 1
---

# Introduction

This section contains miscellaneous configuration options affecting OpenCore operating system loading behaviour in addition to other options that do not readily fit into other sections.

OpenCore broadly follows the “bless” model, also known as the “Apple Boot Policy”. The primary purpose of the “bless” model is to allow embedding boot options within the file system (and be accessible through a specialised driver) as well as supporting a broader range of predefined boot paths as compared to the removable media list set out in the
UEFI specification.

Partitions can only booted by OpenCore when they meet the requirements of a predefined `Scan policy`. This policy sets out which specific file systems a partition must have, and which specific device types a partition must be located on, to be made available by OpenCore as a boot option. Refer to the `ScanPolicy` property for details.

The scan process starts with enumerating all available partitions, filtered based on the `Scan policy`. Each partition may generate multiple primary and alternate options. Primary options represent operating systems installed on the media, while alternate options represent recovery options for the operating systems on the media.

- Alternate options may exist without primary options and vice versa.
- Options may not necessarily represent operating systems on the same partition.
- Each primary and alternate option can be an auxiliary option or not.
    – Refer to the `HideAuxiliary` section for details.
    
The algorithm to determine boot options behaves as follows:

1. Obtain all available partition handles filtered based on the `Scan policy` (and driver availability).
2. Obtain all available boot options from the `BootOrder` UEFI variable.
3. For each boot option found:
    - Retrieve the device path of the boot option.
    - Perform fixups (e.g. NVMe subtype correction) and expansion (e.g. for Boot Camp) of the device path.
    - On failure, if it is an OpenCore custom entry device path, pre-construct the corresponding custom entry and
    succeed.
    - Obtain the device handle by locating the device path of the resulting device path (ignore it on failure).
    - Locate the device handle in the list of partition handles (ignore it if missing).
    - For disk device paths (not specifying a bootloader), execute “bless” (may return > 1 entry).
    - For file device paths, check for presence on the file system directly.
    - Exclude entries if there is a `.contentVisibility` file near the bootloader or inside the boot directory with
    `Disabled` contents (ASCII).
    - Mark device handle as _used_ in the list of partition handles if any.
    - Register the resulting entries as primary options and determine their types.
    The option will become auxiliary for some types (e.g. Apple HFS recovery) or if its `.contentVisibility`
    file contains `Auxiliary`.

4. For each partition handle:
    - If the partition handle is marked as _unused_, execute “bless” primary option list retrieval. In case a `BlessOverride` list is set, both standard and custom “bless” paths will be found.
    - On the OpenCore boot partition, exclude OpenCore bootstrap files using header checks.
    - Register the resulting entries as primary options and determine their types if found.
    
    The option will become auxiliary for some types (e.g. Apple HFS recovery).
    
    - If a partition already has any primary options of the “Apple Recovery” type, proceed to the next handle.
    - Lookup alternate entries by “bless” recovery option list retrieval and predefined paths.
    - Register the resulting entries as alternate auxiliary options and determine their types if found.

5. Custom entries and tools, except such pre-constructed previously, are added as primary options without any checks with respect to `Auxiliary`.
6. System entries, such as `Reset NVRAM`, are added as primary auxiliary options.

The display order of the boot options in the OpenCore picker and the boot process are determined separately from the scanning algorithm.

The display order as follows:

- Alternate options follow corresponding primary options. That is, Apple recovery options will follow the relevant macOS option whenever possible.
- Options will be listed in file system handle firmware order to maintain an established order across reboots regardless of the operating system chosen for loading.
- Custom entries, tools, and system entries will be added after all other options.
- Auxiliary options will only be displayed upon entering “Extended Mode” in the OpenCore picker (typically by pressing the `Space` key).
    
The boot process is as follows:

- Look up the first valid primary option in the `BootNext` UEFI variable.
- On failure, look up the first valid primary option in the `BootOrder` UEFI variable.
- Mark the option as the default option to boot.
- Boot option through the picker or without it depending on the `ShowPicker` option.
- Show picker on failure otherwise.

_Note 1:_ This process will only work reliably when the `RequestBootVarRouting` option is enabled or the firmware does not control UEFI boot options (`OpenDuetPkg` or custom BDS). When `LauncherOption` is not enabled, other operating systems may overwrite OpenCore settings and this property should therefore be enabled when planning to use other operating systems.
_Note 2:_ UEFI variable boot options boot arguments will be removed, if present, as they may contain arguments that can compromise the operating system, which is undesirable when secure boot is enabled.
_Note 3:_ Some operating systems, such as Windows, may create a boot option and mark it as the topmost option upon first boot or after NVRAM resets from within OpenCore. When this happens, the default boot entry choice will remain changed until the next manual reconfiguration.