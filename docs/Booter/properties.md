---
layout: default
title: Properties
parent: Booter
nav_order: 2
---

# Properties

1. `MmioWhitelist`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** To be filled with `plist dict` values, describing addresses critical for particular firmware functioning when DevirtualiseMmio quirk is in use. Refer to the [MmioWhitelist Properties]({{ site.baseurl }}{% link mmiowhitelist.md %}) section below for details.

2. `Patch`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Perform binary patches in booter.
    
    To be filled with `plist dictionary` values, describing each patch. Refer to the [Patch Properties](/docs/Booter/patch.md) section below for details.

3. `Quirks`
    - **Type:** `plist dict`
    - **Description:** Apply individual booter quirks described in the [Quirks Properties](/docs/Booter/quirks.md) section below.