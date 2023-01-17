---
layout: default
title: UEFI and Secure Boot
nav_order: 2
parent: Troubleshooting
---

# UEFI and Secure Boot

OpenCore is designed to provide a secure boot chain between firmware and operating system. On most x86 platforms trusted loading is implemented via [UEFI Secure Boot](https://en.wikipedia.org/wiki/UEFI_Secure_Boot) model. Not only OpenCore fully supports this model, but it also extends its capabilities to ensure sealed configuration via [vaulting]({%link docs/Misc/security.md%}) and provide trusted loading to the operating systems using custom verification, such as [Apple Secure Boot]({%link docs/Misc/security.md %}). Proper secure boot chain requires several steps and careful configuration of certain settings as explained below:

1. Enable Apple Secure Boot by setting `SecureBootModel` to run macOS. Note, that not every macOS is compatible with Apple Secure Boot and there are several other restrictions as explained in Apple Secure Boot section.
2. Disable DMG loading by setting `DmgLoading` to `Disabled` if users have concerns of loading old vulnerable DMG recoveries. This is **not** required, but recommended. For the actual tradeoffs see the details in [DMG loading]({%link docs/Misc/security.md%}) section.
3. Make sure that APFS JumpStart functionality restricts the loading of old vulnerable drivers by setting `MinDate` and `MinVersion` to `0`. More details are provided in [APFS JumpStart]({%link docs/UEFI/apfs_properties.md %}) section. An alternative is to install `apfs.efi` driver manually.
4. Make sure that `Force` driver loading is not needed and all the operating systems are still bootable.
5. Make sure that `ScanPolicy` restricts loading from undesired devices. It is a good idea to prohibit all removable drivers or unknown filesystems.
6. Sign all the installed drivers and tools with the private key. Do not sign tools that provide administrative access to the computer, such as UEFI Shell.
7. Vault the configuration as explained [Vaulting]({%link docs/Misc/security.md %}) section.
8. Sign all OpenCore binaries (`BOOTX64.efi`, `BOOTIa32.efi`, `OpenCore.efi`, custom launchers) used on this system with the same private key.
9. Sign all third-party operating system (not made by Microsoft or Apple) bootloaders if needed. For Linux there is an option to install Microsoft-signed Shim bootloader as explained on e.g. [Debian Wiki](https://wiki.debian.org/SecureBoot).
10. Enable UEFI Secure Boot in firmware preferences and install the certificate with a private key. Details on how to generate a certificate can be found in various articles, such as [this one](https://habr.com/en/post/273497), and are out of the scope of this document. If Windows is needed one will also need to add the [Microsoft Windows Production CA 2011](http://go.microsoft.com/fwlink/?LinkID=321192). To launch option ROMs or to use signed Linux drivers, [Microsoft UEFI Driver Signing CA](http://go.microsoft.com/fwlink/?LinkId=321194) will also be needed.
11. Password-protect changing firmware settings to ensure that UEFI Secure Boot cannot be disabled without the user’s knowledge.