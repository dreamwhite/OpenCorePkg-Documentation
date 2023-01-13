---
layout: default
title: Introduction
parent: Booter
nav_order: 1
---

# Introduction

This section allows the application of different types of UEFI modifications to operating system bootloaders, primarily the Apple bootloader (`boot.efi`). The modifications currently provide various patches and environment alterations for different firmware types. Some of these features were originally implemented as part of [AptioMemoryFix.efi](https://github.com/acidanthera/AptioFixPkg), which is no longer maintained. Refer to the [Tips and Tricks](/docs/Troubleshooting/tips_and_tricks.md) section for instructions on migration.

Booter changes apply with the following effective order:

- `Quirks` are processed.
- `Patch` is processed.

If this is used for the first time on customised firmware, the following requirements should be met before starting:

- Most up-to-date UEFI firmware (check the motherboard vendor website).
- `Fast Boot` and `Hardware Fast Boot` disabled in firmware settings if present.
- `Above 4G Decoding` or similar enabled in firmware settings if present. Note that on some motherboards, notably
the ASUS WS-X299-PRO, this option results in adverse effects and must be disabled. While no other motherboards
with the same issue are known, this option should be checked first whenever erratic boot failures are encountered.
- `DisableIoMapper` quirk enabled, or `VT-d` disabled in firmware settings if present, or ACPI DMAR table deleted.
- **No** ‘slide‘ boot argument present in NVRAM or anywhere else. It is not necessary unless the system cannot be
booted at all or `No slide values are usable! Use custom slide!` message can be seen in the log.
- `CFG Lock` (`MSR 0xE2` write protection) disabled in firmware settings if present. Refer to the [ControlMsrE2](/docs/Kernel/quirks.md) notes
for details.
- `CSM` (Compatibility Support Module) disabled in firmware settings if present. On NVIDIA 6xx/AMD 2xx or older,
GOP ROM may have to be flashed first. Use [GopUpdate](https://www.win-raid.com/t892f16-AMD-and-Nvidia-GOP-update-No-requests-DIY.html) (see the second post) or [AMD UEFI GOP MAKER](http://www.insanelymac.com/forum/topic/299614-asus-eah6450-video-bios-uefi-gop-upgrade-and-gop-uefi-binary-in-efi-for-many-ati-cards/page-1#entry2042163) in case of any potential confusion.
- `EHCI/XHCI Hand-off` enabled in firmware settings `only` if boot stalls unless USB devices are disconnected.
- `VT-x`, `Hyper Threading`, `Execute Disable Bit` enabled in firmware settings if present.
- While it may not be required, sometimes `Thunderbolt support`, `Intel SGX`, and `Intel Platform Trust` may have to be disabled in firmware settings present.

When debugging sleep issues, Power Nap and automatic power off (which appear to sometimes cause wake to black screen or boot loop issues on older platforms) may be temporarily disabled. The specific issues may vary, but ACPI tables should typically be looked at first.

Here is an example of a defect found on some [Z68 motherboards](http://www.insanelymac.com/forum/topic/329624-need-cmos-reset-after-sleep-only-after-login/#entry2534645). To turn Power Nap and the others off, run the following commands in Terminal:

```bash
sudo pmset autopoweroff 0
sudo pmset powernap 0
sudo pmset standby 0
```

_Note_: These settings may be reset by hardware changes and in certain other circumstances. To view their current state, use the `pmset -g` command in Terminal.