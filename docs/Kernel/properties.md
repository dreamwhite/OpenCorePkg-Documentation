---
layout: default
title: Properties
parent: Kernel
nav_order: 2
---

# Properties

1. `Add`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Load selected kernel extensions (kexts) from the `OC/Kexts` directory.
    
    To be filled with `plist dict` values, describing each kext. Refer to the [Add Properties]({%link docs/Kernel/add.md}) section below for details.
    
    _Note 1:_ The load order is based on the order in which the kexts appear in the array. Hence, dependencies must
    appear before kexts that depend on them.

    _Note 2:_ To track the dependency order, inspect the `OSBundleLibraries` key in the `Info.plist` file of the kext being added. Any kext included under the key is a dependency that must appear before the kext being added.
    
    _Note 3:_ Kexts may have inner kexts (`Plugins`) included in the bundle. Such `Plugins` must be added separately and follow the same global ordering rules as other kexts.

2. `Block`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Remove selected kernel extensions (kexts) from the prelinked kernel.
    
    To be filled with `plist dictionary` values, describing each blocked kext. Refer to the [Block Properties]({%link docs/Kernel/block.md}) section below for details.

3. `Emulate`
    - **Type:** `plist dict`
    - **Description:** Emulate certain hardware in kernelspace via parameters described in the [Emulate Properties]({%link docs/Kernel/emulate.md}) section below.

4. `Force`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Load kernel extensions (kexts) from the system volume if they are not cached.
    
    To be filled with `plist dict` values, describing each kext. Refer to the [Force Properties]({%link docs/Kernel/force.md}) section below for details.
    This section resolves the problem of injecting kexts that depend on other kexts, which are not otherwise cached.
    The issue typically affects older operating systems, where various dependency kexts, such as `IOAudioFamily` or `IONetworkingFamily` may not be present in the kernel cache by default.

    _Note 1:_ The load order is based on the order in which the kexts appear in the array. Hence, dependencies must appear before kexts that depend on them.
    
    _Note 2:_ `Force` happens before `Add`.
    
    _Note 3:_ The signature of the “forced” kext is not checked in any way. This makes using this feature extremely dangerous and undesirable for secure boot.
    
    _Note 4:_ This feature may not work on encrypted partitions in newer operating systems.

5. `Patch`
    - **Type:** `plist array`
    - **Failsafe:** Empty
    - **Description:** Perform binary patches in kernel and drivers prior to driver addition and removal.
    
    To be filled with `plist dictionary` values, describing each patch. Refer to the [Patch Properties]({%link docs/Kernel/patch.md}) section below for details.

6. `Quirks`
    - **Type:** `plist dict`
    - **Description:** Apply individual kernel and driver quirks described in the [Quirks Properties]({%link docs/Kernel/quirks.md}) section below.

7. `Scheme`
    - **Type:** `plist dict`
    - **Description:** Define kernelspace operation mode via parameters described in the [Scheme Properties]({%link docs/Kernel/scheme.md}) section below.