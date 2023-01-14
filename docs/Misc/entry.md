---
layout: default
title: Entry
parent: Misc
has_toc: true
nav_order: 7
---

# Entry

1. `Arguments`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used as boot arguments (load options) of the specified entry.

2. `Auxiliary`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set to `true` to hide this entry when `HideAuxiliary` is also set to `true`. Press the `Spacebar` key to enter “Extended Mode” and display the entry when hidden.

3. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide a human readable reference for the entry. Whether this value is used is implementation defined.

4. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set to `true` activate this entry.

5. `Flavour`
    - **Type:** `plist string`
    - **Failsafe:** `Auto`
    - **Description:** Specify the content flavour for this entry. See [OC_ATTR_USE_FLAVOUR_ICON]({%link docs/Misc/boot.md %}) flag for documentation.

6. `FullNvramAccess`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Disable `OpenRuntime` NVRAM protection during usage of a tool.
    
    This disables all of the NVRAM protections provided by `OpenRuntime.efi`, during the time a tool is in use. It should normally be avoided, but may be required for instance if a tool needs to access NVRAM directly without the redirections put in place by `RequestBootVarRouting`.

    _Note:_ This option is only valid for `Tools` and cannot be specified for `Entries` (is always `false`).

7. `Name`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Human readable entry name displayed in the OpenCore picker.

8. `Path`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Entry location depending on entry type.

    - `Entries` specify external boot options, and therefore take device paths in the `Path` key. Care should be exercised as these values are not checked. Example: `PciRoot(0x0)/Pci(0x1,0x1)/.../\EFI\COOL.EFI`
    - `Tools` specify internal boot options, which are part of the bootloader vault, and therefore take file paths
    relative to the `OC/Tools` directory. Example: `OpenShell.efi`.

9. `RealPath`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Pass full path to the tool when launching.

    This should typically be disabled as passing the tool directory may be unsafe with tools that accidentally attempt to access files without checking their integrity. Reasons to enable this property may include cases where tools cannot work without external files or may need them for enhanced functionality such as `memtest86` (for logging and configuration), or `Shell` (for automatic script execution).
    
    _Note:_ This option is only valid for `Tools` and cannot be specified for `Entries` (is always `true`).

10. `TextMode`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Run the entry in text mode instead of graphics mode.
    This setting may be beneficial for some older tools that require text output as all the tools are launched in graphics mode by default. Refer to the [Output Properties]({%link docs/UEFI/input.md %}) section below for information on text modes.