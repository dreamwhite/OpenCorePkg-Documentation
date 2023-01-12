---
layout: default
title: Setup
nav_order: 3
has_children: true
---

# Directory Structure

```
ESP
├── EFI
│   ├── BOOT
│   │   └── BOOTx64.efi
│   └── OC
│       ├── ACPI
│       │   ├── DSDT.aml
│       │   ├── MYTABLE.aml
│       │   └── SSDT-1.aml
│       ├── Drivers
│       │   ├── MyDriver.efi
│       │   └── OtherDriver.efi
│       ├── Kexts
│       │   ├── MyKext.kext
│       │   └── OtherKext.kext
│       ├── Resources
│       │   ├── Audio
│       │   ├── Font
│       │   ├── Image
│       │   └── Label
│       ├── Tools
│       │   └── Tool.efi
│       ├── OpenCore.efi
│       ├── config.plist
│       ├── vault.plist
│       └── vault.sig
├── Kernels
│   ├── kernel
│   ├── kernelcache
│   └── prelinkedkernel
├── boot
├── opencore-YYYY-MM-DD-HHMMSS.txt
└── panic-YYYY-MM-DD-HHMMSS.txt
├── SysReport
├── NVRAM
│   ├── nvram.fallback
│   ├── nvram.plist
│   └── nvram.used
```