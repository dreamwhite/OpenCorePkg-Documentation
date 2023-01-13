---
layout: default
title: Add
parent: Kernel
nav_order: 3
---

# Add

1. `Arch`
    - **Type:** `plist string`
    - **Failsafe:** `Any` (Apply to any supported architecture)
    - **Description:** Kext architecture (`i386`, `x86_64`).
    
2. `BundlePath`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext bundle path (e.g. `Lilu.kext` or `MyKext.kext/Contents/PlugIns/MySubKext.kext`).

3. `Comment`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Arbitrary ASCII string used to provide human readable reference for the entry. Whether this value is used is implementation defined.
- 
4. `Enabled`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Set to `true` to add this kernel extension.

5. `ExecutablePath`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext executable path relative to bundle (e.g. `Contents/MacOS/Lilu`).

6. `MaxKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Adds kernel extension on specified macOS version or older.

    Kernel version can be obtained with `uname -r` command, and should look like 3 numbers separated by dots, for example `18.7.0` is the kernel version for `10.14.6`. Kernel version interpretation is implemented as follows:

    ```
    ParseDarwinVersion(κ, λ, μ) = 
                                κ · 10000 Where κ ∈ (0, 99) is kernel version major
                                + λ · 100 Where λ ∈ (0, 99) is kernel version minor
                                + μ Where μ ∈ (0, 99) is kernel version patch
    ```

    Kernel version comparison is implemented as follows:
    
    - `α=` 
        - _ParseDarwinVersion_(`MinKernel`), If `MinKernel` is valid
        - `0`, _Otherwise_
    - `β=`
        - _ParseDarwinVersion_(`MaxKernel`), If `MaxKernel` is valid
        - `∞` _Otherwise_
    - `γ=`
        -  _ParseDarwinVersion(FindDarwinVersion())_, If valid `Darwin Kernel Version` is found
        - `∞`, _Otherwise_
    
    
    ```
                                f(α,β,γ) = α ≤ γ ≤ β
    ```

    Here _ParseDarwinVersion_ argument is assumed to be 3 integers obtained by splitting Darwin kernel version string from left to right by the `.` symbol. _FindDarwinVersion_ function looks up Darwin kernel version by locating `"Darwin Kernel Version κ.λ.μ"` string in the kernel image.

7. `MinKernel`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Adds kernel extension on specified macOS version or newer.
    
    _Note:_ Refer to the [Add MaxKernel]({%link docs/Kernel/add.md %}) description for matching logic.

8. `PlistPath`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Kext `Info.plist` path relative to bundle (e.g. `Contents/Info.plist`).