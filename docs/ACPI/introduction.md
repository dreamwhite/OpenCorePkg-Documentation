---
layout: default
title: Introduction
nav_order: 1
parent: ACPI
---

# Introduction

ACPI (Advanced Configuration and Power Interface) is an open standard to discover and configure computer hardware.
The [ACPI specification](https://uefi.org/specifications) defines standard tables (e.g. `DSDT`, `SSDT`, `FACS`, `DMAR`) and various methods (e.g. `_DSM`, `_PRW`) for implementation. Modern hardware needs few changes to maintain ACPI compatibility and some options for such changes are provided as part of OpenCore.

To compile and disassemble ACPI tables, the [iASL compiler](https://github.com/acpica/acpica) developed by [ACPICA](https://www.acpica.org) can be used. A GUI front-end to iASL compiler can be downloaded from [Acidanthera/MaciASL](https://github.com/acidanthera/MaciASL/releases).

ACPI changes apply globally (to every operating system) with the following effective order:

- `Delete` is processed.
- `Quirks` are processed.
- `Patch` is processed.
- `Add` is processed.

Applying the changes globally resolves the problems of incorrect operating system detection (consistent with the ACPI specification, not possible before the operating system boots), operating system chainloading, and difficult ACPI debugging. Hence, more attention may be required when writing changes to `_OSI`.

Applying the patches early makes it possible to write so called “proxy” patches, where the original method is patched in the original table and is implemented in the patched table.
There are several sources of ACPI tables and workarounds. Commonly used ACPI tables are provided with OpenCore, VirtualSMC, VoodooPS2, and WhateverGreen releases. Besides those, several third-party instructions may be found on the AppleLife [Laboratory](https://applelife.ru/forums/xakintosh.67) and [DSDT](https://applelife.ru/forums/dsdt.129) subforums (e.g. [Battery register splitting](https://applelife.ru/posts/498967) guide). A slightly more user-friendly explanation of some tables included with OpenCore can also be found in [Dortania](https://dortania.github.io)’s [Getting started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI) guide. For more exotic cases, there are several alternatives such as [daliansky](https://github.com/daliansky)’s [ACPI sample collection](https://github.com/daliansky/OC-little) ([English Translation by 5T33Z0 et al](https://github.com/5T33Z0/OC-Little-Translated)). Please note however, that suggested solutions from third parties may be outdated or may contain errors.