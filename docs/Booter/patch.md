---
layout: default
title: Patch
parent: Booter
nav_order: 4
---

# Patch

1. `Arch`
    - **Type:** `plist string`
    - **Failsafe:** `Any` (Apply to any supported architecture)
    - **Description:** Booter patch architecture (`i386`, `x86_64`).

2. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

3. `Count`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Apply to all occurrences found)
    - **Description:** Number of patch occurrences to apply.

4. `Enabled`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Set to `true` to activate this booter patch.

5. `Find`
    - **Type:** `plist data`
    - **Failsafe:** Empty
    - **Description:** Data to find. Must be equal to `Replace` in size if set.

6. `Identifier`
    - **Type:** `plist string`
    - **Failsafe:** `Any` (Match any booter)
    - **Description:** Apple for macOS booter (typically `boot.efi`); or a name with a suffix, such as `bootmgfw.efi`, for a specific booter.

7. `Limit`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Search the entire booter)
    - **Description:** Maximum number of bytes to search for.

8. `Mask`
    - **Type:** `plist data`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Data bitwise mask used during find comparison. Allows fuzzy search by ignoring not masked (set to zero) bits. Must be equal to `Find` in size if set.

9. `Replace`
    - **Type:** `plist data`
    - **Failsafe:** Empty
    - **Description:** Replacement data of one or more bytes.

10. `ReplaceMask`
    - **Type:** `plist data`
    - **Failsafe:** Empty (Ignored)
    - **Description:** Data bitwise mask used during replacement. Allows fuzzy replacement by updating masked (set to non-zero) bits. Must be equal to `Replace` in size if set.
    
11. `Skip`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Do not skip any occurrences)
    - **Description:** Number of found occurrences to skip before replacements are applied.