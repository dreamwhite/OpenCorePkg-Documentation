---
layout: default
title: Tools and Applications
nav_order: 3
parent: UEFI
---

# Tools and Applications

Standalone tools may help to debug firmware and hardware. Some of the known tools are listed below. While some tools can be launched from within OpenCore (Refer to the [Tools]({%link docs/Misc/properties.md %}) subsection for more details), most should be run separately either directly or from `Shell`.

To boot into OpenShell or any other tool directly save `OpenShell.efi` under the name of `EFI\BOOT\BOOTX64.EFI` on a FAT32 partition. It is typically unimportant whether the partition scheme is `GPT` or `MBR`.

While the previous approach works both on Macs and other computers, an alternative Mac-only approach to bless the tool on an HFS+ or APFS volume:

```bash
sudo bless --verbose --file /Volumes/VOLNAME/DIR/OpenShell.efi \ --folder /Volumes/VOLNAME/DIR/ --setBoot
```

_Note 1:_ `/System/Library/CoreServices/BridgeVersion.bin` should be copied to `/Volumes/VOLNAME/DIR`.
_Note 2:_ To be able to use the `bless` command, disabling [System Integrity Protection](https://developer.apple.com/library/archive/documentation/Security/Conceptual/System_Integrity_Protection_Guide/ConfiguringSystemIntegrityProtection/ConfiguringSystemIntegrityProtection.html) is necessary.
_Note 3:_ To be able to boot [Secure Boot](https://support.apple.com/HT208330) might be disabled if present.

Some of the known tools are listed below (builtin tools are marked with `*`):

[BootKicker](https://github.com/acidanthera/OpenCorePkg)* - Display Apple BootPicker menu (for Macs with compatible firmware).
[ChipTune](https://github.com/acidanthera/OpenCorePkg)* - Test BeepGen protocol and generate audio signals of different style and length.
[CleanNvram](https://github.com/acidanthera/OpenCorePkg)* - Reset NVRAM alternative bundled as a standalone tool.
[CsrUtil](https://github.com/acidanthera/OpenCorePkg)* - Simple implementation of SIP-related features of Apple `csrutil`.
[GopStop](https://github.com/acidanthera/OpenCorePkg)* - Test GraphicsOutput protocol with a [simple scenario](https://github.com/acidanthera/OpenCorePkg/tree/master/Application/GopStop).
[KeyTester](https://github.com/acidanthera/OpenCorePkg)* - Test keyboard input in `SimpleText` mode.
[MemTest86](https://www.memtest86.com) - Memory testing utility.
[OpenControl](https://github.com/acidanthera/OpenCorePkg)* - Unlock and lock back NVRAM protection for other tools to be able to get full NVRAM access when launching from OpenCore.
[OpenShell](https://github.com/acidanthera/OpenCorePkg)* - OpenCore-configured [`UEFI Shell`](http://github.com/tianocore/edk2) for compatibility with a broad range of firmware. 
[PavpProvision](https://github.com/acidanthera/OpenCorePkg) - Perform EPID provisioning (requires certificate data configuration).
[ResetSystem](https://github.com/acidanthera/OpenCorePkg)* - Utility to perform system reset. Takes reset type as an argument: `coldreset`, `firmware`, `shutdown`, `warmreset`. Defaults to `coldreset`.
[RtcRw](https://github.com/acidanthera/OpenCorePkg)* - Utility to read and write RTC (CMOS) memory.
[ControlMsrE2](https://github.com/acidanthera/OpenCorePkg)* - Check `CFG Lock` (MSR `0xE2` write protection) consistency across all cores and change such hidden options on selected platforms.
[TpmInfo](https://github.com/acidanthera/OpenCorePkg)* Check Intel PTT (Platform Trust Technology) capability on the platform, which allows using fTPM 2.0 if enabled. The tool does not check whether fTPM 2.0 is actually enabled.