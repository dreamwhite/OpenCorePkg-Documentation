---
layout: default
title: Tips and Tricks
nav_order: 5
parent: Troubleshooting
---

# Tips and Tricks

1. **How do I debug boot failures?**

Obtaining the actual error message is usually adequate. For this, ensure that:
- A `DEBUG` or `NOOPT` version of OpenCore is used.
- Logging is enabled (1) and shown onscreen (2): `Misc → Debug → Target = 3`.
- Logged messages from at least `DEBUG_ERROR` (`0x80000000`), `DEBUG_WARN` (`0x00000002`), and `DEBUG_INFO` (`0x00000040`) levels are visible onscreen: Misc → Debug → DisplayLevel = `0x80000042`.
- Critical error messages, such as `DEBUG_ERROR`, stop booting: `Misc → Security → HaltLevel = 0x80000000`.
- Watch Dog is disabled to prevent automatic reboot: `Misc → Debug → DisableWatchDog = true`.
- Boot Picker (entry selector) is enabled: `Misc → Boot → ShowPicker = true`.

If there is no obvious error, check the available workarounds in the `Quirks` sections one by one. For early boot troubleshooting, for instance, when OpenCore menu does not appear, using `UEFI Shell` (bundled with OpenCore) may help to see early debug messages.

2. **How do I debug macOS boot failures?**

- Refer to `boot-args` values such as `debug=0x100`, `keepsyms=1`, `-v`, and similar.
- Do not forget about `AppleDebug` and `ApplePanic` properties.
- For macOS to correctly recognise and set up serial ports, the `CustomPciSerialDevice` quirk may be enabledwhen a PCI serial port card is installed.
- Take care of `Booter`, `Kernel`, and `UEFI` quirks.
- Consider using serial port to inspect early kernel boot failures. For this `debug=0x108`, `serial=5`, and `msgbuf=1048576` boot arguments are needed. Refer to the patches in `Sample.plist` when dying before serial init.
- Always read the logs carefully.

3. **How do I customise boot entries?**

OpenCore follows standard Apple Bless model and extracts the entry name from `.contentDetails` and `.disk_label.contentDetails` files in the booter directory if present. These files contain an ASCII string with an entry title, which may then be customised by the user.

4. **How do I choose the default boot entry?**

OpenCore uses the primary UEFI boot option to select the default entry. This choice can be altered from UEFI Setup, with the macOS [Startup Disk](https://support.apple.com/HT202796) preference, or the Windows [Boot Camp](https://support.apple.com/guide/bootcamp-control-panel/start-up-your-mac-in-windows-or-macos-bcmp29b8ac66/mac) Control Panel. Since choosing OpenCore’s `BOOTx64.EFI` as a primary boot option limits this functionality in addition to several types of firmware deleting incompatible boot options, potentially including those created by macOS, users are strongly encouraged to use the `RequestBootVarRouting` quirk, which will preserve the selection made in the operating system within the OpenCore variable space. Note, that `RequestBootVarRouting` requires a separate driver for functioning.

5. **What is the simplest way to install macOS?**

Copy online recovery image (`*.dmg` and `*.chunklist` files) to `com.apple.recovery.boot` directory on a FAT32 partition with OpenCore. Load the OpenCore picker and choose the entry, it will have a `(dmg)` suffix. Custom name may be created by providing `.contentDetails` file.

To download recovery online [macrecovery.py](https://github.com/acidanthera/OpenCorePkg/blob/master/Utilities/macrecovery/macrecovery.py) can be used.

For offline installation refer to [How to create a bootable installer for macOS](https://support.apple.com/HT201372) article. Apart from App Store and `softwareupdate` utility there also are [third-party utilities](https://github.com/corpnewt/gibMacOS) to download an offline image.

6. **Why do online recovery images (*.dmg) fail to load?**

This may be caused by missing HFS+ driver, as all presently known recovery volumes have HFS+ filesystem.

7. **Can I use this on Apple hardware or virtual machines?**

Yes. Virtual machines and most relatively modern Mac models, as far back as the `MacPro3,1`, are fully supported. While specific detail relevant to Mac hardware is often limited, some ongoing instructions can be found on [MacRumors.com](https://forums.macrumors.com/threads/2207814).

8. **Why must Find&Replace patches be equal in size?**

For machine code (x86 code) it is not possible to do differently sized replacements due to [relative addressing](https://en.wikipedia.org/w/index.php?title=Relative_addressing). For ACPI code this is risky, and is technically equivalent to ACPI table replacement, thus not implemented. More detailed explanation can be found on [AppleLife.ru](https://applelife.ru/posts/819790) or in the ACPI section of this document.

9. **How can I decide which Booter quirks to use?**

These quirks originate from `AptioMemoryFix` driver but provide a wider set of changes specific to modern systems. Note, that `OpenRuntime` driver is required for most configurations. To get a configuration similar to `AptioMemoryFix` the following set of quirks should be enabled:

- `ProvideConsoleGop` (UEFI quirk)
- `AvoidRuntimeDefrag`
- `DiscardHibernateMap`
- `EnableSafeModeSlide`
- `EnableWriteUnprotector`
- `ForceExitBootServices`
- `ProtectMemoryRegions`
- `ProvideCustomSlide`
- `RebuildAppleMemoryMap`
- `SetupVirtualMap`

However, as of today, such set is strongly discouraged as some of these quirks are not necessary to be enabled or need additional quirks. For example, `DevirtualiseMmio` and `ProtectUefiServices` are often required, while `DiscardHibernateMap` and `ForceExitBootServices` are rarely necessary.

Unfortunately for some quirks such as `RebuildAppleMemoryMap`, `EnableWriteUnprotector`, `ProtectMemoryRegions`, `SetupVirtualMap`, and `SyncRuntimePermissions` there is no definite approach even on similar systems, so trying all their combinations may be required for optimal setup. Refer to individual quirk descriptions in this document for details.