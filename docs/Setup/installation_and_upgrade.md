---
layout: default
title: Installation and Upgrade
parent: Setup
nav_order: 2
---
# Installation and Upgrade

To install OpenCore, replicate the [Configuration Structure](/docs/configuration/configuration_structure.md) described in the previous section in the EFI volume of a GPT partition. While corresponding sections of this document provide some information regarding external resources such as ACPI tables, UEFI drivers, or kernel extensions (kexts), completeness of the matter is out of the scope of this document. Information about kernel extensions may be found in a separate [Kext List](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Kexts.md) document available in the OpenCore repository. Vaulting information is provided in the [Security Properties](/docs/misc/security.md) section of this document.
8

The `OC config` file, as with any property list file, can be edited with any text editor, such as nano or vim. However, specialised software may provide a better experience. On macOS, the preferred GUI application is [Xcode](https://developer.apple.com/xcode). The [ProperTree](https://github.com/corpnewt/ProperTree) editor is a lightweight, cross-platform and open-source alternative.

It is strongly recommended to avoid configuration creation tools that are aware of the internal configuration structure as this may result in invalid configurations (since the structure gets constantly updated). If such tools are to be used despite this warning, ensure that only stable versions of OpenCore explicitly supported by such tools are used. In such cases, the use of open-source implementations with transparent binary generation (such as [OCAT](https://github.com/ic005k/QtOpenCoreConfig)) is encouraged, given that other tools may contain malware. In addition, configurations created for a specific hardware setup should never be used on different hardware setups.

For BIOS booting, a third-party UEFI environment provider is required and `OpenDuetPkg` is one such UEFI environment provider for legacy systems. To run OpenCore on such a legacy system, `OpenDuetPkg` can be installed with a dedicated tool — BootInstall (bundled with OpenCore). Third-party utilities can be used to perform this on systems other than macOS.

For upgrade purposes, refer to the `Differences.pdf` document which provides information about changes to the configuration (as compared to the previous release) as well as to the `Changelog.md` document (which contains a list of modifications across all published updates).