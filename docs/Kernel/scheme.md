---
layout: default
title: Scheme
parent: Kernel
nav_order: 9
---

# Scheme

These properties are particularly relevant for older macOS operating systems. Refer to the Legacy Apple OS section for details on how to install and troubleshoot such macOS installations.

1. `CustomKernel`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Use customised kernel cache from the `Kernels` directory located at the root of the ESP partition.
    
    Unsupported platforms including `Atom` and `AMD` require modified versions of XNU kernel in order to boot. This option provides the possibility to using a customised kernel cache which contains such modifications from ESP partition.

2. `FuzzyMatch`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Use `kernelcache` with different checksums when available.
    
    On macOS 10.6 and earlier, `kernelcache` filename has a checksum, which essentially is `adler32` from SMBIOS product name and EfiBoot device path. On certain firmware, the EfiBoot device path differs between UEFI and macOS due to ACPI or hardware specifics, rendering `kernelcache` checksum as always different.
    
    This setting allows matching the latest `kernelcache` with a suitable architecture when the `kernelcache` without suffix is unavailable, improving macOS 10.6 boot performance on several platforms.

3. `KernelArch`
    **Type:** `plist string`
    **Failsafe:** `Auto` (Choose the preferred architecture automatically)
    **Description:** Prefer specified kernel architecture (`i386`, `i386-user32`, `x86_64`) when available.

    On macOS 10.7 and earlier, the XNU kernel can boot with architectures different from the usual `x86_64`. This setting will use the specified architecture to boot macOS when it is supported by the macOS and the configuration:

    - `i386` — Use `i386` (32-bit) kernel when available.
    - `i386-user32` — Use `i386` (32-bit) kernel when available and force the use of 32-bit userspace on 64-bit
    capable processors if supported by the operating system.
    – On macOS, 64-bit capable processors are assumed to support `SSSE3`. This is not the case for older 64-bit capable Pentium processors, which cause some applications to crash on macOS 10.6. This behaviour corresponds to the `-legacy` kernel boot argument.
    – This option is unavailable on macOS 10.4 and 10.5 when running on 64-bit firmware due to an uninitialised 64-bit segment in the XNU kernel, which causes AppleEFIRuntime to incorrectly execute 64-bit code as 16-bit code.
    - `x86_64` — Use `x86_64` (64-bit) kernel when available.

    The algorithm used to determine the preferred kernel architecture is set out below.
    1. `arch` argument in image arguments (e.g. when launched via UEFI Shell) or in `boot-args` variable overrides any compatibility checks and forces the specified architecture, completing this algorithm.
    2. OpenCore build architecture restricts capabilities to `i386` and `i386-user32` mode for the 32-bit firmware variant.
    3. Determined EfiBoot version restricts architecture choice:
    
    - 10.4-10.5 — `i386` or `i386-user32` (only on 32-bit firmware)
    - 10.6 — `i386`, `i386-user32`, or `x86_64`
    - 10.7 — `i386` or `x86_64`
    - 10.8 or newer — `x86_64`
    
    4. If `KernelArch` is set to `Auto` and `SSSE3` is not supported by the CPU, capabilities are restricted to `i386-user32` if supported by EfiBoot.
    5. Board identifier (from SMBIOS) based on EfiBoot version disables `x86_64` support on an unsupported model if any `i386` variant is supported. `Auto` is not consulted here as the list is not overridable in EfiBoot.
    6. `KernelArch` restricts the support to the explicitly specified architecture (when not set to `Auto`) if the architecture remains present in the capabilities.
    7. The best supported architecture is chosen in this order: `x86_64`, `i386`, `i386-user32`.

    Unlike macOS 10.7 (where certain board identifiers are treated as the `i386` only machines), and macOS 10.5 or earlier (where `x86_64` is not supported by the macOS kernel), macOS 10.6 is very special. The architecture choice on macOS 10.6 depends on many factors including not only the board identifier, but also the macOS product type (client vs server), macOS point release, and amount of RAM. The detection of all these is complicated and impractical, as several point releases had implementation flaws resulting in a failure to properly execute the server detection in the first place. For this reason, OpenCore on macOS 10.6 falls back on the `x86_64` architecture whenever it is supported by the board, as it is on macOS 10.7.

    A 64-bit Mac model compatibility matrix corresponding to actual EfiBoot behaviour on macOS 10.6.8 and 10.7.5 is outlined below.


    | **Model** 	| **10.6 (Minimal)** 	| **10.6 (Client)** 	| **10.6 (Server)** 	| **10.7 (Any)** 	|
    |---	|---	|---	|---	|---	|
    | Macmini 	| 4,x (Mid 2010) 	| 5,x (Mid 2011) 	| 4,x (Mid 2010) 	| 3,x (Early 2009) 	|
    | MacBook 	| Unsupported 	| Unsupported 	| Unsupported 	| 5,x (2009/09) 	|
    | MacBookAir 	| Unsupported 	| Unsupported 	| Unsupported 	| 2,x (Late 2008) 	|
    | MacBookPro 	| 4,x (Early 2008) 	| 8,x (Early 2011) 	| 8,x (Early 2011) 	| 3,x (Mid 2007) 	|
    | iMac 	| 8,x (Early 2008) 	| 12,x (Mid 2011) 	| 12,x (Mid 2011) 	| 7,x (Mid 2007) 	|
    | MacPro 	| 3,x (Early 2008) 	| 5,x (Mid 2010) 	| 3,x (Early 2008) 	| 3,x (Early 2008) 	|
    | Xserve 	| 2,x (Early 2008) 	| 2,x (Early 2008) 	| 2,x (Early 2008) 	| 2,x (Early 2008) 	|

    Note: `3+2` and `6+4` hotkeys to choose the preferred architecture are unsupported as they are handled by EfiBoot and hence, difficult to detect.

4. `KernelCache`
    **Type:** `plist string`
    **Failsafe:** `Auto`
    **Description:** Prefer specified kernel cache type (`Auto`, `Cacheless`, `Mkext`, `Prelinked`) when available.

    Different variants of macOS support different kernel caching variants designed to improve boot performance. This setting prevents the use of faster kernel caching variants if slower variants are available for debugging and stability reasons. That is, by specifying `Mkext`, `Prelinked` will be disabled for e.g. 10.6 but not for 10.7.
    The list of available kernel caching types and its current support in OpenCore is listed below.

    | **macOS** 	| **i386 NC** 	| **i386 MK** 	| **i386 PK** 	| **x86_64 NC** 	| **x86_64 MK** 	| **x86_64 PK** 	| **x86_64 KC** 	|
    |---	|---	|---	|---	|---	|---	|---	|---	|
    | 10.4 	| YES 	| YES (V1) 	| NO (V1) 	| - 	| - 	| - 	| - 	|
    | 10.5 	| YES 	| YES (V1) 	| NO (V1) 	| - 	| - 	| - 	| - 	|
    | 10.6 	| YES 	| YES (V2) 	| YES(V2) 	| YES 	| YES(V2) 	| YES(V2) 	| - 	|
    | 10.7 	| YES 	| - 	| YES(V3) 	| YES 	| - 	| YES(V3) 	| - 	|
    | 10.8-10.9 	| - 	| - 	| - 	| YES 	| - 	| YES(V3) 	| - 	|
    | 10.10-10.15 	| - 	| - 	| - 	| - 	| - 	| YES(V3) 	| - 	|
    | 11+ 	| - 	| - 	| - 	| - 	| - 	| YES(V3) 	| YES 	|


    Note: The first version (V1) of the 32-bit `prelinkedkernel` is unsupported due to the corruption of kext symbol tables by the tools. On this version, the `Auto` setting will block `prelinkedkernel` booting. This also results in the `keepsyms=1` boot argument being non-functional for kext frames on these systems.