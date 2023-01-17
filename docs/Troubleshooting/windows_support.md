---
layout: default
title: Windows Support
nav_order: 3
parent: Troubleshooting
---

# Windows Support

## Can I install Windows?

While no official Windows support is provided, 64-bit UEFI Windows installations (Windows 8 and above) prepared with Boot Camp are supposed to work. Third-party UEFI installations as well as systems partially supporting UEFI boot, such as Windows 7, might work with some extra precautions. Things to consider:

- MBR (Master Boot Record) installations are legacy and will not be supported.
- All the modifications applied (to ACPI, NVRAM, SMBIOS, etc.) are supposed to be operating system agnostic, i.e. apply equally regardless of the OS booted. This enables Boot Camp software experience on Windows.
- macOS requires the first partition to be EFI System Partition, and does not support the default Windows layout. While OpenCore does have a [workaround](https://github.com/acidanthera/bugtracker/issues/327) for this, it is highly recommend not to rely on it and install properly.
- Windows may need to be reactivated. To avoid it consider setting SystemUUID to the original firmware UUID. Be aware that it may be invalid on old firmware, i.e., not random. If there still are issues, consider using HWID or KMS38 license or making the use `Custom UpdateSMBIOSMode`. Other nuances of Windows activation are out of the scope of this document and can be found online.

## What additional software do I need?

To enable operating system switching and install relevant drivers in the majority of cases Windows support software from [Boot Camp](https://support.apple.com/boot-camp) is required. For simplicity of the download process or when configuring an already installed Windows version a third-party utility, [Brigadier](https://github.com/timsutton/brigadier), can be used successfully. Note, that [7-Zip](https://www.7-zip.org) may be downloaded and installed prior to using Brigadier.

Remember to always use the latest version of Windows support software from Boot Camp, as versions prior to 6.1 do not support APFS, and thus will not function correctly. To download newest software pass most recent Mac model to Brigadier, for example `./brigadier.exe -m iMac19,1`. To install Boot Camp on an unsupported Mac model afterwards run PowerShell as Administrator and enter `msiexec /i BootCamp.msi`. If there is a previous version of Boot Camp installed it should be removed first by running `msiexec /x BootCamp.msi` command. `BootCamp.msi` file is located in `BootCamp/Drivers/Apple` directory and can be reached through Windows Explorer.

While Windows support software from Boot Camp solves most of compatibility problems, the rest may still have to be addressed manually:
- To invert mouse wheel scroll direction `FlipFlopWheel` must be set to `1` as explained on [SuperUser](https://superuser.com/a/364353).
- `RealTimeIsUniversal` must be set to `1` to avoid time desync between Windows and macOS as explained on [SuperUser](https://superuser.com/q/494432) (this is typically not required).
- To access Apple filesystems such as HFS+ and APFS, separate software may need to be installed. Some of the known utilities are: [Apple HFS+ driver](https://forums.macrumors.com/threads/apple-hfs-windows-driver-download.1368010/) ([workaround for Windows 10](https://forums.macrumors.com/threads/apple-hfs-windows-driver-download.1368010/post-24180079)), HFSExplorer, MacDrive, Paragon APFS, Paragon HFS+, TransMac, etc. Remember to never ever attempt to modify Apple file systems from Windows as this often leads to irrecoverable data loss.

## Why do I see Basic data partition in the Boot Camp Startup Disk control panel?

The Boot Camp control panel uses the GPT partition table to obtain each boot option name. After installing Windows separately, the partition has to be relabelled manually. This can be done with many utilities including the open-source [gdisk](https://sourceforge.net/projects/gptfdisk) utility. Reference example:

```
PS C:\gdisk> .\gdisk64.exe \\.\physicaldrive0
GPT fdisk (gdisk) version 1.0.4

Command (? for help): p
Disk \\.\physicaldrive0: 419430400 sectors, 200.0 GiB
Sector size (logical): 512 bytes
Disk identifier (GUID): DEC57EB1-B3B5-49B2-95F5-3B8C4D3E4E12 Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33 First usable sector is 34, last usable sector is 419430366 Partitions will be aligned on 2048-sector boundaries
Total free space is 4029 sectors (2.0 MiB)

Number  Start (sector) End (sector)  Size       Code  Name
  1              2048      023999   499.0 MiB   2700  Basic data partition
  2           1024000     1226751    99.0 MiB   EF00  EFI system partition
  3           1226752     1259519    16.0 MiB   0C01  Microsoft reserved ...
  4           1259520   419428351   199.4 GiB   0700  Basic data partition

Command (? for help): c
Partition number (1-4): 4
Enter name: BOOTCAMP

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to \\.\physicaldrive0.
Disk synchronization succeeded! The computer should now use the new partition table.
The operation has completed successfully.
```

## How do I choose Windows BOOTCAMP with custom NTFS drivers?

Third-party drivers providing NTFS support, such as [NTFS-3G](https://www.tuxera.com/community/open-source-ntfs-3g), Paragon NTFS, Tuxera NTFS or [Seagate Paragon Driver](https://www.seagate.com/support/software/paragon) disrupt certain macOS functionality, including the [Startup Disk](https://support.apple.com/HT202796) preference pane normally used for operating system selection. While the recommended option remains not to use such drivers as they commonly corrupt the filesystem, and prefer the driver bundled with macOS with optional write support ([command](http://osxdaily.com/2013/10/02/enable-ntfs-write-support-mac-os-x) or [GUI](https://mounty.app)), there still exist vendor-specific workarounds for their products: [Tuxera](https://www.tuxera.com/products/tuxera-ntfs-for-mac/faq), [Paragon](https://kb.paragon-software.com/article/6604), etc.