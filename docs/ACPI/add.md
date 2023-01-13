---
layout: default
title: Add
nav_order: 3
parent: ACPI
---

# Add

1. `Comment`
    - **Type**: `plist string`
    - **Failsafe**: Empty
    - **Description**: Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.

2. `Enabled`
    - **Type**: `plist boolean`
    - **Failsafe**: `false`
    - **Description**: Set to `true` to add this ACPI table.

3. `Path`
    - **Type**: `plist string`
    - **Failsafe**: Empty
    - **Description**: File paths meant to be loaded as ACPI tables. Example values include `DSDT.aml`, `SubDir/SSDT-8.aml`, `SSDT-USBX.aml`, etc.

The ACPI table load order follows the item order in the array. ACPI tables are loaded from the `OC/ACPI` directory.

Note: All tables apart from tables with a `DSDT` table identifier (determined by parsing data, not by filename)
insert new tables into the ACPI stack. `DSDT` tables perform a replacement of DSDT tables instead.
