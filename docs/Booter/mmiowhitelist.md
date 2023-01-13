---
layout: default
title: MmioWhitelist Properties
parent: Booter
nav_order: 3
---

# MmioWhitelist Properties

1. `Address`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Exceptional MMIO address, which memory descriptor should be left virtualised (unchanged) by `DevirtualiseMmio`. This means that the firmware will be able to directly communicate with this memory region during operating system functioning, because the region this value is in will be assigned a virtual address.
    The addresses written here must be part of the memory map, have `EfiMemoryMappedIO` type and `EFI_MEMORY_RUNTIME` attribute (highest bit) set. The debug log can be used to find the list of the candidates.

2. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

3. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Exclude MMIO address from the devirtualisation procedure.