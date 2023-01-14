---
layout: default
title: Properties
parent: NVRAM
nav_order: 2
---

# Properties

1. `Add`
    - **Type:** `plist dict`
    - **Description:** Sets NVRAM variables from a map (`plist dict`) of GUIDs to a map (`plist dict`) of variable names and their values in `plist multidata` format. GUIDs must be provided in canonic string format in upper or lower case (e.g. `8BE4DF61-93CA-11D2-AA0D-00E098032B8C`).
    
The `EFI_VARIABLE_BOOTSERVICE_ACCESS` and `EFI_VARIABLE_RUNTIME_ACCESS` attributes of created variables are set. Variables will only be set if not present or deleted. That is, to overwrite an existing variable value, add the variable name to the `Delete` section. This approach enables the provision of default values until the operating system takes the lead.
    
_Note:_ The implementation behaviour is undefined when the `plist key` does not conform to the GUID format.

2. `Delete`
    - **Type:** `plist dict`
    - **Description:** Removes NVRAM variables from a map (`plist dict`) of GUIDs to an array (`plist array`) of variable names in `plist string` format.

3. `LegacyOverwrite`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Permits overwriting firmware variables from `nvram.plist`.
    
    _Note:_ Only variables accessible from the operating system will be overwritten.

4. `LegacySchema`
    - **Type:** `plist dict`
    - **Description:** Allows setting certain NVRAM variables from a map (`plist dict`) of GUIDs to an array (plist array) of variable names in plist string format.
    
    * value can be used to accept all variables for certain GUID.
    
    **WARNING:** Choose variables carefully, as the `nvram.plist` file is not vaulted. For instance, do not include `boot-args` or `csr-active-config`, as these can be used to bypass SIP.

5. `WriteFlash`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enables writing to flash memory for all added variables.

    _Note:_ This value should be enabled on most types of firmware but is left configurable to account for firmware that may have issues with NVRAM variable storage garbage collection or similar.

The `nvram` command can be used to read NVRAM variable values from macOS by concatenating the GUID and name variables separated by a `:` symbol. For example, `nvram 7C436110-AB2A-4BBB-A880-FE41995C9F82:boot-args`.

A continuously updated variable list can be found in a corresponding document: [NVRAM Variables](https://docs.google.com/spreadsheets/d/1HTCBwfOBkXsHiK7os3b2CUc6k68axdJYdGl-TyXqLu0).
