---
layout: default
title: Delete
nav_order: 4
---

# Delete

1. `All`
    - **Type:** `plist boolean`
    - **Failsafe:** `false` (Only delete the first matched table)
    - **Description:** Set to `true` to delete all ACPI tables matching the condition.
2. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.
3. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set to `true` to remove this ACPI table.
4. `OemTableId`
     - **Type:** `plist data`, 8 bytes
     - **Failsafe:** All zero (Match any table OEM ID) Description: Match table OEM ID equal to this value.
5. `TableLength`
    - **Type:** `plist integer`
    - **Failsafe:** `0` (Match any table size)
    - **Description:** Match table size equal to this value.
6. `TableSignature`
    - **Type:** `plist data`, 4 bytes
    - **Failsafe:** All zero (Match any table signature) Description: Match table signature equal to this value.

_Note_: Do not use table signatures when the sequence must be replaced in multiple places. This is particularly relevant when performing different types of renames.