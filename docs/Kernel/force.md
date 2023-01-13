---
layout: default
title: Force
parent: Kernel
nav_order: 6
---

# Force

1. `Arch`
    - **Type:** `plist string`
    - **Failsafe:** `Any` (Apply to any supported architecture)
    - **Description:** Kext architecture (`i386`, `x86_64`).

2. `BundlePath`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext bundle path (e.g. `System\Library\Extensions\IONetworkingFamily.kext`).

3. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

4. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set to `true` to load this kernel extension from the system volume when not present in the kernel cache.

5. `ExecutablePath`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext executable path relative to bundle (e.g. `Contents/MacOS/IONetworkingFamily`).

6. `Identifier`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext identifier to perform presence checking before adding (e.g. `com.apple.iokit.IONetworkingFamily`). Only drivers which identifiers are not be found in the cache will be added.

7. `MaxKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Adds kernel extension on specified macOS version or older.
    
    Note: Refer to the [Add MaxKernel]({%link docs/Kernel/add.md}) description for matching logic.

8. `MinKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Adds kernel extension on specified macOS version or newer.
    
    Note: Refer to the [Add MaxKernel]({%link docs/Kernel/add.md}) description for matching logic.

9. `PlistPath`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext `Info.plist` path relative to bundle (e.g. `Contents/Info.plist`).