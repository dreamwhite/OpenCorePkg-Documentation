---
layout: default
title: Drivers Properties
nav_order: 14
parent: UEFI
---

# Drivers Properties

1. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

2. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** If false this driver entry will be ignored.

3. `Path`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Path of file to be loaded as a UEFI driver from `OC/Drivers` directory.

4. `LoadEarly`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Load the driver early in the OpenCore boot process, before NVRAM setup.
    
    _Note:_ Do not enable this option unless specifically recommended to do so for a given driver and purpose.

5. `Arguments`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Some OpenCore plugins accept optional additional arguments which may be specified as a string here.