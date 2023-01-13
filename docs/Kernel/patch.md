---
layout: default
title: Patch
parent: Kernel
nav_order: 7
---

# Patch

1. `Arch`
    - **Type:** `plist string`
    - **Failsafe:** Any (Apply to any supported architecture)
    - **Description:** Kext patch architecture (`i386`, `x86_64`).

2. `Base`
    - **Type:** `plist string`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Selects symbol-matched base for patch lookup (or immediate replacement) by obtaining the address of the provided symbol name.

3. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

4. `Count`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Number of patch occurrences to apply. `0` applies the patch to all occurrences found.

5. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** This kernel patch will not be used unless set to true.

6. `Find`
    - **Type:** `plist data`
    - **Failsafe:** Empty (Immediate replacement at `Base`)
    - **Description:** Data to find. Must be equal to `Replace` in size if set.

7. `Identifier`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext bundle identifier (e.g. `com.apple.driver.AppleHDA`) or `kernel` for kernel patch.

8. `Limit`
    - **Type:** `plist integer`
    - **Failsafe:** 0 (Search entire kext or kernel)
    - **Description:** Maximum number of bytes to search for.

9. `Mask`
    - **Type:** `plist data`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Data bitwise mask used during find comparison. Allows fuzzy search by ignoring not masked (set to zero) bits. Must be equal to `Replace` in size if set.

10. `MaxKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Patches data on specified macOS version or older.
    
    Note: Refer to the [Add MaxKernel]({%link docs/Kernel/add.md %}) description for matching logic.

11. `MinKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Patches data on specified macOS version or newer.
    
    Note: Refer to the [Add MaxKernel]({%link docs/Kernel/add.md %}) description for matching logic.

12. `Replace`
    - **Type:** `plist data`
    - **Failsafe:** Empty
    - **Description:** Replacement data of one or more bytes.

13. `ReplaceMask`
    - **Type:** `plist data`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Data bitwise mask used during replacement. Allows fuzzy replacement by updating masked (set to non-zero) bits. Must be equal to `Replace` in size if set.

14. `Skip`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Do not skip any occurrences)
    - **Description:** Number of found occurrences to skip before replacements are applied.