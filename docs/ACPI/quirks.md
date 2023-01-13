---
layout: default
title: Quirks
nav_order: 6
parent: ACPI
---

# Quirks

1. `FadtEnableReset`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Provide reset register and flag in FADT table to enable reboot and shutdown.

    Mainly required on legacy hardware and a few newer laptops. Can also fix power-button shortcuts. Not recommended unless required.

2. `NormalizeHeaders`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Cleanup ACPI header fields to workaround macOS ACPI implementation flaws that result in boot crashes. Reference: [Debugging AppleACPIPlatform on 10.13](https://alextjam.es/debugging-appleacpiplatform/) by Alex James (also known as theracermaster). The issue was fixed in macOS Mojave (10.14).

3. `RebaseRegions`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Attempt to heuristically relocate ACPI memory regions. Not recommended.

    ACPI tables are often generated dynamically by the underlying firmware implementation. Among the position- independent code, ACPI tables may contain the physical addresses of MMIO areas used for device configuration, typically grouped by region (e.g. `OperationRegion`). Changing firmware settings or hardware configuration, upgrading or patching the firmware inevitably leads to changes in dynamically generated ACPI code, which sometimes results in the shift of the addresses in the aforementioned `OperationRegion` constructions.

    For this reason, the application of modifications to ACPI tables is extremely risky. The best approach is to make as few changes as possible to ACPI tables and to avoid replacing any tables, particularly DSDT tables. When this cannot be avoided, ensure that any custom DSDT tables are based on the most recent DSDT tables or attempt to remove reads and writes for the affected areas.
    When nothing else helps, this option could be tried to avoid stalls at `PCI Configuration Begin` phase of macOS booting by attempting to fix the ACPI addresses. It is not a magic bullet however, and only works with the most typical cases. Do not use unless absolutely required as it can have the opposite effect on certain platforms and result in boot failures.

4. `ResetHwSig`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Reset `FACS` table `HardwareSignature` value to `0`.

    This works around firmware that fail to maintain hardware signature across the reboots and cause issues with waking from hibernation.

5. `ResetLogoStatus`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Reset `BGRT` table `Displayed` status field to `false`.

    This works around firmware that provide a `BGRT` table but fail to handle screen updates afterwards.

6. `SyncTableIds`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Sync table identifiers with the `SLIC` table.

    This works around patched tables becoming incompatible with the `SLIC` table causing licensing issues in older Windows operating systems.