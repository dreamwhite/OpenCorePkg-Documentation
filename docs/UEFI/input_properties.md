---
layout: default
title: Input Properties
nav_order: 15
parent: UEFI
---

# Input Properties

1. `KeyFiltering`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable keyboard input sanity checking.

    Apparently some boards such as the GA Z77P-D3 may return uninitialised data in `EFI_INPUT_KEY` with all input protocols. This option discards keys that are neither ASCII, nor are defined in the UEFI specification (see tables 107 and 108 in version 2.8).

2. `KeyForgetThreshold`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Treat duplicate key presses as held keys if they arrive during this timeout, in 10 ms units. Only applies to systems using `KeySupport`.
    
    `AppleKeyMapAggregator` protocol is supposed to contain a fixed length buffer of currently pressed keys. However, the majority of the drivers which require `KeySupport` report key presses as interrupts, with automatically generated key repeat behaviour with some defined initial and subsequent delay. As a result, to emulate the raw
    key behaviour required by several Apple boot systems, we use a timeout to merge multiple repeated keys which are submitted within a small timeout window.
    
    This option allows setting this timeout based on the platform. The recommended value for the majority of platforms is from `5` (`50` milliseconds) to `7` (`70` milliseconds), although values up to `9` (`90` milliseconds) have been observed to be required on some PS/2 systems. For reference, holding a key on VMware will repeat roughly every 20 milliseconds and the equivalent value for APTIO V is `30-40` milliseconds. `KeyForgetThreshold` should be configured to be longer than this. Thus, it is possible to configure a lower `KeyForgetThreshold` value on platforms with a faster native driver key repeat rate, for more responsive input, and it is required to set a higher value on slower platforms.
    
    Pressing keys one after the other results in delays of at least `60` and `100` milliseconds for the same platforms. Ideally, `KeyForgetThreshold` should remain lower than this value, to avoid merging real key presses.
    
    Tuning the value of `KeyForgetThreshold` is necessary for accurate and responsive keyboard input on systems on which `KeySupport` is enabled, and it is recommended to follow the instructions below to tune it correctly for your system.
    
    _Note 1:_ To tune `KeyForgetThreshold`, you may use the ‘set default’ indicator within either OpenCanopy or the builtin picker. If `KeyForgetThreshold` is too low then the ‘set default’ indicator will continue to flicker while `CTRL` or `=/+` is held down. You should configure the lowest value which avoids this flicker. On some systems (e.g. Aptio IV and potentially other systems using `AMI KeySupport` mode) you will be able to find a minimum `KeyForgetThreshold` value at which the ‘set default’ indicator goes on and stays on with no flicker at all - if so, use this value. On most other systems using KeySupport, you will find that the ‘set default’ indicator will flicker once, when first pressing and holding the CTRL or =/+ key, and then after a further very brief interval will go on and stay on. On such systems, you should chose the lowest value of `KeyForgetThreshold` at which you see only one initial flicker and then no subsequent flickering. (Where this happens, it is an unavoidable artefect on those systems of using KeySupport to emulate raw keyboard data, which is not made available by UEFI.)
    _Note 2:_ `KeyForgetThreshold` should never need to be more than about `9` or `10` at most. If it is set to a value much higher than this, it will result in noticeably unresponsive keyboard input. Therefore, for overall key responsiveness, it is strongly recommended to configure a relatively lower value, at which the ‘set default’ indicator flickers once and then does not flicker, rather than using a much higher value (i.e. significantly greater than `10`), which you may be able to find but should not use, where the ‘set default’ indicator does not flicker at all.

3. `KeySupport`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable internal keyboard input translation to `AppleKeyMapAggregator` protocol.
    
    This option activates the internal keyboard interceptor driver, based on `AppleGenericInput`, also known as AptioInputFix, to fill the `AppleKeyMapAggregator` database for input functioning. In cases where a separate driver such as `OpenUsbKbDxe` is used, this option should never be enabled. Additionally, this option is not required and should not be enabled with Apple firmware.
    
4. `KeySupportMode`
    - **Type:** `plist string`
    - **Failsafe:** `Auto`
    - **Description:** Set internal keyboard input translation to `AppleKeyMapAggregator` protocol mode.
    
    - `Auto` — Performs automatic choice as available with the following preference: `AMI`, `V2`, `V1`.
    - `V1` — Uses UEFI standard legacy input protocol `EFI_SIMPLE_TEXT_INPUT_PROTOCOL`.
    - `V2` — Uses UEFI standard modern input protocol `EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL`.
    - `AMI` — Uses APTIO input protocol `AMI_EFIKEYCODE_PROTOCOL`.

    _Note:_ Currently `V1`, `V2`, and `AMI` unlike `Auto` only do filtering of the particular specified protocol. This may change in the future versions.

5. `KeySwap`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Swap `Command` and `Option` keys during submission.

    This option may be useful for keyboard layouts with `Option` key situated to the right of `Command` key.

6. `PointerSupport`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable internal pointer driver.

    This option implements standard UEFI pointer protocol (`EFI_SIMPLE_POINTER_PROTOCOL`) through certain OEM protocols. The option may be useful on Z87 ASUS boards, where `EFI_SIMPLE_POINTER_PROTOCOL` is defective.

7. `PointerSupportMode`
    - **Type:** `plist string`
    - **Failsafe:** Empty
    - **Description:** Set OEM protocol used for internal pointer driver.
    Currently the only supported variant is `ASUS`, using specialised protocol available on certain Z87 and Z97 ASUS boards. More details can be found in [`LongSoft/UefiTool#116`](https://github.com/LongSoft/UEFITool/pull/116). The value of this property cannot be empty if `PointerSupport` is enabled.

8. `TimerResolution`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Set architecture timer resolution.

    This option allows updating the firmware architecture timer period with the specified value in `100` nanosecond units. Setting a lower value typically improves performance and responsiveness of the interface and input handling.

    The recommended value is `50000` (5 milliseconds) or slightly higher. Select ASUS Z87 boards use `60000` for the interface. Apple boards use `100000`. In case of issues, this option can be left as `0` to not change the timer resolution.