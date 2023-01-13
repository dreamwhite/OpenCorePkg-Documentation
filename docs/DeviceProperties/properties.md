---
layout: default
title: Properties
parent: DeviceProperties
nav_order: 2
---


# Properties


1. `Add`
    - **Type:** `plist dict`
    - **Description:** Sets device properties from a map (`plist dict`) of device paths to a map (`plist dict`) of variable names and their values in `plist multidata` format.

    _Note 1:_ Device paths must be provided in canonic string format (e.g. `PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)`).
    _Note 2:_ Existing properties will not be changed unless deleted in the `DeviceProperties Delete` section.

2. `Delete`
    - **Type:** `plist dict`
    - **Description:** Removes device properties from a map (`plist dict`) of device paths to an array (`plist array`) of variable names in `plist string` format.
    
    _Note:_ Currently, existing properties may only exist on firmware with DeviceProperties drivers (e.g. Apple). Hence, there is typically no reason to delete variables unless a new driver has been installed