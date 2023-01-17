---
layout: default
title: AppleInput Properties
nav_order: 12
parent: UEFI
---

# AppleInput Properties

1. `AppleEvent`
    - **Type:** `plist string`
    - **Failsafe:** `Auto`
    - **Description:** Determine whether the OpenCore builtin or the OEM Apple Event protocol is used.

    This option determines whether the OEM Apple Event protocol is used (where available), or whether OpenCore’s reversed engineered and updated re-implementation is used. In general OpenCore’s re-implementation should be preferred, since it contains updates such as noticeably improved fine mouse cursor movement and configurable key repeat delays.
    
    - `Auto` — Use the OEM Apple Event implementation if available, connected and recent enough to be used, otherwise use the OpenCore re-implementation. On non-Apple hardware, this will use the OpenCore builtin implementation. On some Macs such as Classic Mac Pros, this will prefer the Apple implementation but on both older and newer Mac models than these, this option will typically use the OpenCore re-implementation instead. On older Macs, this is because the implementation available is too old to be used while on newer Macs, it is because of optimisations added by Apple which do not connect the Apple Event protocol except when needed – e.g. except when the Apple boot picker is explicitly started. Due to its somewhat unpredicatable results, this option is not typically recommended.
    - `Builtin` — Always use OpenCore’s updated re-implementation of the Apple Event protocol. Use of this setting is recommended even on Apple hardware, due to improvements (better fine mouse control, configurable key delays) made in the OpenCore re-implementation of the protocol.
    - `OEM` — Assume Apple’s protocol will be available at driver connection. On all Apple hardware where a recent enough Apple OEM version of the protocol is available – whether or not connected automatically by Apple’s firmware – this option will reliably access the Apple implementation. On all other systems, this option will result in no keyboard or mouse support. For the reasons stated, `Builtin` is recommended in preference to this option in most cases.

2. `CustomDelays`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable custom key repeat delays when using the OpenCore re-implementation of the Apple Event protocol. Has no effect when using the OEM Apple implementation (see `AppleEvent` setting).
    
    - `true` — The values of `KeyInitialDelay` and `KeySubsequentDelay` are used.
    - `false` — Apple default values of 500ms (`50`) and 50ms (`5`) are used.

3. `KeyInitialDelay`
    - **Type:** `plist integer`
    - **Failsafe:** `50` (500ms before first key repeat)
    - **Description:** Configures the initial delay before keyboard key repeats in the OpenCore re-implementation of the Apple Event protocol, in units of 10ms.
    
    The Apple OEM default value is `50` (500ms).
    
    _Note 1:_ On systems not using `KeySupport`, this setting may be freely used to configure key repeat behaviour.
    
    _Note 2:_ On systems using `KeySupport`, but which do not show the ‘two long delays’ behavior (see Note `3`) and/or which always show a solid ‘set default’ indicator (see `KeyForgetThreshold`) then this setting may also be freely used to configure key repeat initial delay behaviour, except that it should never be set to less than `KeyForgetThreshold` to avoid uncontrolled key repeats.
    
    _Note 3:_ On some systems using `KeySupport`, you may find that you see one additional slow key repeat before normal speed key repeat starts, when holding a key down. If so, you may wish to configure `KeyInitialDelay` and `KeySubsequentDelay` according to the instructions at Note `3` of `KeySubsequentDelay`.

4. `KeySubsequentDelay`
    - **Type:** `plist integer`
    - **Failsafe:** `5` (50ms between subsequent key repeats)
    - **Description:** Configures the gap between keyboard key repeats in the OpenCore re-implementation of the Apple Event protocol, in units of 10ms.
    The Apple OEM default value is `5` (50ms). `0` is an invalid value for this option (will issue a debug log warning and use `1` instead).
    
    _Note 1:_ On systems not using `KeySupport`, this setting may be freely used to configure key repeat behaviour.
    _Note 2:_ On systems using `KeySupport`, but which do not show the ‘two long delays’ behaviour (see Note `3`) and/or which always show a solid ‘set default’ indicator (see KeyForgetThreshold) (which should apply to many/most systems using  `AMI KeySupport` mode) then this setting may be freely used to configure key repeat subsequent delay behaviour, except that it should never be set to less than KeyForgetThreshold to avoid uncontrolled key repeats.
    _Note 3:_ On some systems using `KeySupport`, particularly `KeySupport` in non-AMI mode, you may find that after configuring `KeyForgetThreshold` you get one additional slow key repeat before normal speed key repeat starts, when holding a key down. On systems where this is the case, it is an unavoidable artefect of using `KeySupport` to emulate raw keyboard data, which is not made available by UEFI. While this ‘two long delays’ issue has minimal effect on overall usability, nevertheless you may wish to resolve it, and it is possible to do so as follows:

    - Set `CustomDelays` to `true`
    - Set `KeyInitialDelay` to `0`
    - Set `KeySubsequentDelay` to at least the value of your `KeyForgetThreshold` setting
    
    The above procedure works as follows:

    - Setting `KeyInitialDelay` to `0` cancels the Apple Event initial repeat delay (when using the OpenCore builtin Apple Event implementation with `CustomDelays` enabled), therefore the only long delay you will see is the the non-configurable and non-avoidable initial long delay introduced by the BIOS key support on these machines.
    - Key-smoothing parameter `KeyForgetThreshold` effectively acts as the shortest time for which a key can appear to be held, therefore a key repeat delay of less than this will guarantee at least one extra repeat for every key press, however quickly the key is physically tapped.
    - In the unlikely event that you still get frequent, or occasional, double key responses after setting `KeySubsequentDelay` equal to your system’s value of `KeyForgetThreshold`, then increase `KeySubsequentDelay` by one or two more until this effect goes away.

5. `GraphicsInputMirroring`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Apple’s own implementation of AppleEvent prevents keyboard input during graphics applications from appearing on the basic console input stream.
    
    With the default setting of `false`, OpenCore’s builtin implementation of AppleEvent replicates this behaviour.
    On non-Apple hardware this can stop keyboard input working in graphics-based applications such as Windows BitLocker which use non-Apple key input methods. The recommended setting on all hardware is true.
    _Note:_ AppleEvent’s default behaviour is intended to prevent unwanted queued keystrokes from appearing after exiting graphics-based UEFI applications; this issue is already handled separately within OpenCore.
    
    - `true` — Allow keyboard input to reach graphics mode apps which are not using Apple input protocols.
    - `false` — Prevent key input mirroring to non-Apple protocols when in graphics mode.

6. `PointerPollMin`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Configure minimal pointer polling period in ms.

    This is the minimal period the OpenCore builtin AppleEvent driver polls pointer devices (e.g. mice, trackpads) for motion events. The current implementation defaults to 10 ms. Setting `0` leaves this default unchanged.
    
    _Note:_ The OEM Apple implementation uses a polling rate of 2 ms.

7. `PointerPollMax`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Configure maximum pointer polling period in ms.

    This is the maximum period the OpenCore builtin AppleEvent driver polls pointer devices (e.g. mice, trackpads) for motion events. The period is increased up to this value as long as the devices do not respond in time. The current implementation defaults to 80 ms. Setting `0` leaves this default unchanged.
    
    Certain trackpad drivers often found in Dell laptops can be very slow to respond when no physical movement happens. This can affect OpenCanopy and FileVault 2 user interface responsiveness and loading times. Increasing the polling periods can reduce the impact.
    
    _Note:_ The OEM Apple implementation uses a polling rate of 2 ms.

8. `PointerPollMask`
    - **Type:** `plist integer`, 32 bit
    - **Failsafe:** `-1`
    - **Description:** Configure indices of polled pointers.
    
    Selects pointer devices to poll for AppleEvent motion events. `-1` implies all devices. A bit sum is used to determine particular devices. E.g. to enable devices `0`, `2`, `3` the value will be `1+4+8` (corresponding powers of two). A total of 32 configurable devices is supported.

    Certain pointer devices can be present in the firmware even when no corresponding physical devices are available. These devices usually are placeholders, aggregate devices, or proxies. Gathering information from these devices may result in inaccurate motion activity in the user interfaces and even cause performance issues. Disabling such pointer devices is recommended for laptop setups having issues of this kind.

    The amount of pointer devices available in the system can be found in the log. Refer to `Found N pointer` devices message for more details.

    _Note:_ Has no effect when using the OEM Apple implementation (see `AppleEvent` setting).

9. `PointerSpeedDiv`
    - **Type:** `plist integer`
    - **Failsafe:** `1`
    - **Description:** Configure pointer speed divisor in the OpenCore re-implementation of the Apple Event protocol. Has no effect when using the OEM Apple implementation (see `AppleEvent` setting).

    Configures the divisor for pointer movements. The Apple OEM default value is `1`. `0` is an invalid value for this option.
    
    _Note:_ The recommended value for this option is `1`. This value may optionally be modified in combination with `PointerSpeedMul`, according to user preference, to achieve customised mouse movement scaling.

10. `PointerSpeedMul`
    - **Type:** `plist integer`
    - **Failsafe:** `1`
    - **Description:** Configure pointer speed multiplier in the OpenCore re-implementation of the Apple Event protocol. Has no effect when using the OEM Apple implementation (see `AppleEvent` setting).

    Configures the multiplier for pointer movements. The Apple OEM default value is `1`.
    
    _Note:_ The recommended value for this option is `1`. This value may optionally be modified in combination with `PointerSpeedDiv`, according to user preference, to achieve customised mouse movement scaling.

11. `PointerDwellClickTimeout`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Configure pointer dwell-clicking single left click timeout in milliseconds in the OpenCore re- implementation of the Apple Event protocol. Has no effect when using the OEM Apple implementation (see `AppleEvent` setting).
    
    When the timeout expires, a single left click is issued at the current position. `0` indicates the timeout is disabled.

12. `PointerDwellDoubleClickTimeout`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Configure pointer dwell-clicking single left double click timeout in milliseconds in the OpenCore re-implementation of the Apple Event protocol. Has no effect when using the OEM Apple implementation (see `AppleEvent` setting).
    
    When the timeout expires, a single left double click is issued at the current position. `0` indicates the timeout is disabled.

13. `PointerDwellRadius`
    - **Type:** `plist integer`
    - **Failsafe:** `0`
    - **Description:** Configure pointer dwell-clicking tolerance radius in pixels in the OpenCore re-implementation of the Apple Event protocol. Has no effect when using the OEM Apple implementation (see `AppleEvent` setting).
    
    The radius is scaled by `UIScale`. When the pointer leaves this radius, the timeouts for `PointerDwellClickTimeout` and `PointerDwellDoubleClickTimeout` are reset and the new position is the centre for the new dwell-clicking tolerance radius.