---
layout: default
title: APFS Properties
nav_order: 11
parent: UEFI
---

# APFS Properties

1. `EnableJumpstart`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Load embedded APFS drivers from APFS containers.

    An APFS EFI driver is bundled in all bootable APFS containers. This option performs the loading of signed APFS drivers (consistent with the ScanPolicy). Refer to the “EFI Jumpstart” section of the [Apple File System Reference](https://developer.apple.com/support/apple-file-system/Apple-File-System-Reference.pdf) for details.

2. `GlobalConnect`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Perform full device connection during APFS loading.

    Every handle is connected recursively instead of the partition handle connection typically used for APFS driver loading. This may result in additional time being taken but can sometimes be the only way to access APFS partitions on certain firmware, such as those on older HP laptops.

3. `HideVerbose`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Hide verbose output from APFS driver.

    APFS verbose output can be useful for debugging.

4. `JumpstartHotPlug`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Load APFS drivers for newly connected devices.

    Permits APFS USB hot plug which enables loading APFS drivers, both at OpenCore startup and during OpenCore picker display. Disable if not required.

5. `MinDate`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Minimal allowed APFS driver date.

    The APFS driver date connects the APFS driver with the calendar release date. Apple ultimately drops support for older macOS releases and APFS drivers from such releases may contain vulnerabilities that can be used to compromise a computer if such drivers are used after support ends. This option permits restricting APFS drivers to current macOS versions.

    - `0` — require the default supported release date of APFS in OpenCore. The default release date will increase with time and thus this setting is recommended. Currently set to `2021/01/01`.
    - `-1` — permit any release date to load (strongly discouraged).
    - Other — use custom minimal APFS release date, e.g. `20200401` for `2020/04/01`. APFS release dates can be found in OpenCore boot log and [`OcApfsLib`](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Library/OcApfsLib.h).

6. `MinVersion`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Minimal allowed APFS driver version.

    The APFS driver version connects the APFS driver with the macOS release. Apple ultimately drops support for older macOS releases and APFS drivers from such releases may contain vulnerabilities that can be used to compromise a computer if such drivers are used after support ends. This option permits restricting APFS drivers to current macOS versions.

    - `0` — require the default supported version of APFS in OpenCore. The default version will increase with time and thus this setting is recommended. Currently set to allow macOS Big Sur and newer (`1600000000000000`).
    - `-1` — permit any version to load (strongly discouraged).
    - Other — use custom minimal APFS version, e.g. `1412101001000000` from macOS Catalina 10.15.4. APFS versions can be found in OpenCore boot log and [`OcApfsLib`](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Library/OcApfsLib.h).