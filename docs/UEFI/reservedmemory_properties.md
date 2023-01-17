---
layout: default
title: ReservedMemory Properties
nav_order: 19
parent: UEFI
---

# ReservedMemory Properties

1. `Address`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Start address of the reserved memory region, which should be allocated as reserved effectively marking the memory of this type inaccessible to the operating system.

    The addresses written here must be part of the memory map, have a `EfiConventionalMemory` type, and be page-aligned (4 KBs).
    
    _Note:_ Some types of firmware may not allocate memory areas used by S3 (sleep) and S4 (hibernation) code unless CSM is enabled causing wake failures. After comparing the memory maps with CSM disabled and enabled, these areas can be found in the lower memory and can be fixed up by doing the reservation. Refer to the `Sample.plist` file for details.

2. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

3. `Size`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Size of the reserved memory region, must be page-aligned (4 KBs).

4. `Type`
    - **Type:** `plist string`
    - **Failsafe:** `Reserved`
    - **Description:** Memory region type matching the UEFI specification memory descriptor types. Mapping:
    
    - `Reserved` — `EfiReservedMemoryType`
    - `LoaderCode` — `EfiLoaderCode`
    - `LoaderData` — `EfiLoaderData`
    - `BootServiceCode` — `EfiBootServicesCode`
    - `BootServiceData` — `EfiBootServicesData`
    - `RuntimeCode` — `EfiRuntimeServicesCode`
    - `RuntimeData` — `EfiRuntimeServicesData`
    - `Available` — `EfiConventionalMemory`
    - `Persistent` — `EfiPersistentMemory`
    - `UnusableMemory` — `EfiUnusableMemory`
    - `ACPIReclaimMemory` — `EfiACPIReclaimMemory`
    - `ACPIMemoryNVS` — `EfiACPIMemoryNVS`
    - `MemoryMappedIO` — `EfiMemoryMappedIO`
    - `MemoryMappedIOPortSpace` — `EfiMemoryMappedIOPortSpace`
    - `PalCode` — `EfiPalCode`

5. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** This region will not be reserved unless set to `true`.