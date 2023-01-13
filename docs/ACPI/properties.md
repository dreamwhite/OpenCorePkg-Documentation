---
layout: default
title: Properties
nav_order: 2
parent: ACPI
---

# Properties

1. `Add`
    - **Type**: `plist array`
    - **Failsafe**: Empty
    - **Description**: Load selected tables from the OC/ACPI directory.

    To be filled with `plist dict` values, describing each add entry. Refer to the [Add Properties](/docs/ACPI/add.md) section below for details.

2. `Delete`
    - **Type**: `plist array`
    - **Failsafe**: Empty
    - **Description**: Remove selected tables from the ACPI stack.

    To be filled with plist dict values, describing each delete entry. Refer to the [Delete Properties](/docs/ACPI/delete.md) section below for details.

3. `Patch`
    - **Type**: plist array
    - **Failsafe**: Empty
    - **Description**: Perform binary patches in ACPI tables before table addition or removal.

    To be filled with plist dictionary values describing each patch entry. Refer to the [Patch Properties](/docs/ACPI/patch.md) section below for details.

4. `Quirks`
    - **Type**: plist dict
    - **Description**: Apply individual ACPI quirks described in the [Quirks Properties](/docs/ACPI/quirks.md) section below.