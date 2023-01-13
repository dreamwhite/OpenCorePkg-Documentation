---
layout: default
title: Block
parent: Kernel
nav_order: 4
---

# Block

1. `Arch`
    - **Type:** `plist string`
    - **Failsafe:** `Any` (Apply to any supported architecture)
    - **Description:** Kext block architecture (`i386`, `x86_64`).

2. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

3. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set to `true` to block this kernel extension.

4. `Identifier`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext bundle identifier (e.g. `com.apple.driver.AppleTyMCEDriver`).

5. `MaxKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Blocks kernel extension on specified macOS version or older.

    _Note:_ Refer to the [Add MaxKernel]({%link docs/Kernel/add.md}) description for matching logic.

6. `MinKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Blocks kernel extension on specified macOS version or newer.

7. `Strategy`
    - **Type:** `plist string`
    - **Failsafe:** Disable (Forcibly make the kernel driver kmod startup code return failure)
    - **Description:** Determines the behaviour of kernel driver blocking.

    Valid values:

        - `Disable` — Forcibly make the kernel driver kmod startup code return failure.
        - `Exclude` — Remove the kernel driver from the kernel cache by dropping plist entry and filling in zeroes.
        
    _Note:_ It is risky to `Exclude` a kext that is a dependency of others.

    _Note 2:_ At this moment `Exclude` is only applied to `prelinkedkernel` and newer mechanisms.
    
    _Note 3:_ In most cases strategy `Exclude` requires the new kext to be injected as a replacement.