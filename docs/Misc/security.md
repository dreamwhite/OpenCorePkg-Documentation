---
layout: default
title: Security
parent: Misc
has_toc: true
nav_order: 5
---

# Security

1. `AllowSetDefault`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Allow `CTRL+Enter` and `CTRL+Index` handling to set the default boot option in the OpenCore picker.

    _Note 1:_ May be used in combination with `Shift+Enter` or `Shift+Index` when `PollAppleHotKeys` is enabled.
    _Note 2:_ In order to support systems with unresponsive modifiers during preboot (which includes `V1` and `V2` `KeySupport` mode on some firmware) OpenCore also allows holding the `=/+` key in order to trigger ‘set default’ mode.

2. `ApECID`
    - **Type:** `plist integer`, 64 bit
    - **Failsafe:** `0`
    - **Description:** Apple Enclave Identifier.
    
    Setting this value to any non-zero 64-bit integer will allow using personalised Apple Secure Boot identifiers. To use this setting, generate a random 64-bit number with a cryptographically secure random number generator.
    As an alternative, the first 8 bytes of `SystemUUID` can be used for `ApECID`, this is found in macOS 11 for Macs without the T2 chip.
    
    With this value set and `SecureBootModel` valid (and not `Disabled`), it is possible to achieve [Full Security](https://support.apple.com/en-us/HT208330) of Apple Secure Boot.
    
    To start using personalised Apple Secure Boot, the operating system must be reinstalled or personalised. Until the operating system is personalised, only macOS DMG recovery can be loaded. In cases where DMG recovery is missing, it can be downloaded by using the `macrecovery` utility and saved in `com.apple.recovery.boot` as explained in the Tips and Tricks section. Note that [DMG loading]({%link docs/Misc/security.md %}) needs to be set to `Signed` to use any DMG with Apple Secure Boot.
    
    To personalise an existing operating system, use the bless command after loading to macOS DMG recovery. Mount the system volume partition, unless it has already been mounted, and execute the following command:
    
    ```bash
    bless --folder "/Volumes/Macintosh HD/System/Library/CoreServices" \
        --bootefi --personalize
    ```

    On macOS 11 and newer the dedicated `x86legacy` model always uses ApECID. When this configuration setting is left as 0 first 8 bytes of `system-id` variable are used instead.

    On macOS versions before macOS 11, which introduced a dedicated `x86legacy` model for models without the T2 chip, personalised Apple Secure Boot may not work as expected. When reinstalling the operating system, the macOS Installer from macOS 10.15 and older will often run out of free memory on the `/var/tmp` partition when trying to install macOS with the personalised Apple Secure Boot. Soon after downloading the macOS installer image, an `Unable to verify macOS` error message will appear.
    
    To workaround this issue, allocate a dedicated RAM disk of 2 MBs for macOS personalisation by entering the following commands in the macOS recovery terminal before starting the installation:
    
    ```bash
    disk=$(hdiutil attach -nomount ram://4096)
    diskutil erasevolume HFS+ SecureBoot $disk
    diskutil unmount $disk
    mkdir /var/tmp/OSPersonalizationTemp
    diskutil mount -mountpoint /var/tmp/OSPersonalizationTemp $disk
    ```

3. `AuthRestart`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable `VirtualSMC`-compatible authenticated restart.
    Authenticated restart is a way to reboot FileVault 2 enabled macOS without entering the password. A dedicated terminal command can be used to perform authenticated restarts: `sudo fdesetup authrestart`. It is also used when installing operating system updates.
    
    VirtualSMC performs authenticated restarts by splitting and saving disk encryption keys between NVRAM and RTC, which despite being removed as soon as OpenCore starts, may be considered a security risk and thus is optional.

4. `BlacklistAppleUpdate`
    **Type:** `plist boolean`
    **Failsafe:** `false`
    **Description:** Ignore boot options trying to update Apple peripheral firmware (e.g. MultiUpdater.efi).
    
    _Note:_ Certain operating systems, such as macOS Big Sur, are incapable of disabling firmware updates by using the `run-efi-updater` NVRAM variable.

5. `DmgLoading`
    - **Type:** `plist string`
    - **Failsafe:** `Signed`
    - **Description:** Define Disk Image (DMG) loading policy used for macOS Recovery.
    
    Valid values:

    - `Disabled` — loading DMG images will fail. The `Disabled` policy will still let the macOS Recovery load in most cases as typically, there are `boot.efi` files compatible with Apple Secure Boot. Manually downloaded DMG images stored in `com.apple.recovery.boot` directories will not load, however.
    - `Signed` — only Apple-signed DMG images will load. Due to the design of Apple Secure Boot, the `Signed` policy will let any Apple-signed macOS Recovery load regardless of the Apple Secure Boot state, which may not always be desired. While using signed DMG images is more desirable, verifying the image signature may slightly slow the boot time down (by up to 1 second).
    - `Any` — any DMG images will mount as normal filesystems. The `Any` policy is strongly discouraged and will result in boot failures when Apple Secure Boot is active.

6. `EnablePassword`
    - **Type:** `plist boolean`
    - **Failsafe:** `false`
    - **Description:** Enable password protection to facilitate sensitive operations.

    Password protection ensures that sensitive operations such as booting a non-default operating system (e.g. macOS recovery or a tool), resetting NVRAM storage, trying to boot into a non-default mode (e.g. verbose mode or safe mode) are not allowed without explicit user authentication by a custom password. Currently, password and salt are hashed with 5000000 iterations of SHA-512.
    
    _Note:_ This functionality is still under development and is not ready for production environments.

7. `ExposeSensitiveData`
    - **Type:** `plist integer`
    - **Failsafe:** `0x6`
    - **Description:** Sensitive data exposure bitmask (sum) to operating system.

    - `0x01` — Expose the printable booter path as a UEFI variable.
    - `0x02` — Expose the OpenCore version as a UEFI variable.
    - `0x04` — Expose the OpenCore version in the OpenCore picker menu title.
    - `0x08` — Expose OEM information as a set of UEFI variables.

    The exposed booter path points to OpenCore.efi or its booter depending on the load order. To obtain the booter path, use the following command in macOS:

    ```bash
    nvram 4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102:boot-path
    ```

    To use a booter path to mount a booter volume, use the following command in macOS:
    
    ```bash
    u=$(nvram 4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102:boot-path | sed 's/.*GPT,\([^,]*\),.*/\1/'); \ 
      if [ "$u" != "" ]; then sudo diskutil mount $u ; fi
    ```

    To obtain the current OpenCore version, use the following command in macOS:

    ```bash
    nvram 4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102:opencore-version
    ```

    If the OpenCore version is not exposed the variable will contain `UNK-000-0000-00-00` sequence. To obtain OEM information, use the following commands in macOS:
    
    ```bash
    nvram 4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102:oem-product # SMBIOS Type1 ProductName
    nvram 4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102:oem-vendor # SMBIOS Type2 Manufacturer
    nvram 4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102:oem-board # SMBIOS Type2 ProductName
    ```
    
8. `HaltLevel`
    - **Type:** `plist integer`, 64 bit
    - **Failsafe:** `0x80000000` (`DEBUG_ERROR`)
    - **Description:** EDK II debug level bitmask (sum) causing CPU to halt (stop execution) after obtaining a message of `HaltLevel`. Possible values match `DisplayLevel` values.
    
    _Note 1:_ A halt will only occur if bit 0 (i.e. enable logging) for Target under section Misc->Debug is set.
    _Note 2:_ A halt will only occur after the configuration is loaded and logging is configured. If any log messages occur at the specified halt level in early log (i.e. before this), they will cause a halt when they are flushed to the log once it has been configured.

9. `PasswordHash`
    - **Type:** `plist data` 64 bytes
    - **Failsafe:** all zero
    - **Description:** Password hash used when `EnablePassword` is set.

10. `PasswordSalt`
    - **Type:** `plist data`
    - **Failsafe:** empty
    - **Description:** Password salt used when `EnablePassword` is set.

11. `Vault`
    - **Type:** `plist string`
    - **Failsafe:** Secure
    - **Description:** Enables the OpenCore vaulting mechanism.
    
    Valid values:
    - `Optional` — require nothing, no vault is enforced, insecure.
    - `Basic` — require `vault.plist` file present in OC directory. This provides basic filesystem integrity verification
    and may protect from unintentional filesystem corruption.
    - `Secure` — require `vault.sig` signature file for `vault.plist` in OC directory. This includes `Basic` integrity checking but also attempts to build a trusted bootchain.
    
    The `vault.plist` file should contain SHA-256 hashes for all files used by OpenCore. The presence of this file is highly recommended to ensure that unintentional file modifications (including filesystem corruption) do not go unnoticed. To create this file automatically, use the [create_vault.sh](https://github.com/acidanthera/OpenCorePkg/tree/master/Utilities/CreateVault) script. Notwithstanding the underlying file system, the path names and cases between `config.plist` and `vault.plist` must match.

    The `vault.sig` file should contain a raw 256 byte RSA-2048 signature from a SHA-256 hash of vault.plist. The signature is verified against the public key embedded into `OpenCore.efi`.

    To embed the public key, either one of the following should be performed:
    
    - Provide public key during the OpenCore.efi compilation in OpenCoreVault.c file.
    - Binary patch OpenCore.efi replacing zeroes with the public key between `=BEGIN OC VAULT=` and `==END OC VAULT==` ASCII markers.
    
    The RSA public key 520 byte format description can be found in Chromium OS documentation. To convert the public key from X.509 certificate or from PEM file use [RsaTool](https://github.com/acidanthera/OpenCorePkg/tree/master/Utilities/CreateVault).
    
    The complete set of commands to:
    - Create `vault.plist`.
    - Create a new RSA key (always do this to avoid loading old configuration). - Embed RSA key into OpenCore.efi.
    - Create `vault.sig`.

    Can look as follows:
    
    ```bash
    cd /Volumes/EFI/EFI/OC
    /path/to/create_vault.sh .
    /path/to/RsaTool -sign vault.plist vault.sig vault.pub
    off=$(($(strings -a -t d OpenCore.efi | grep "=BEGIN OC VAULT=" | cut -f1 -d' ')+16)) dd of=OpenCore.efi if=vault.pub bs=1 seek=$off count=528 conv=notrunc
    rm vault.pub
    ```

    _Note 1:_ While it may appear obvious, an external method is required to verify `OpenCore.efi` and `BOOTx64.efi` for secure boot path. For this, it is recommended to enable UEFI SecureBoot using a custom certificate and to sign `OpenCore.efi` and `BOOTx64.efi` with a custom key. More details on customising secure boot on modern firmware can be found in the [Taming UEFI SecureBoot](https://habr.com/post/273497/) paper (in Russian).
    _Note 2:_ Regardless of this option, `vault.plist` is always used when present, and both `vault.plist` and `vault.sig` are used and required when a public key is embedded into OpenCore.efi, and errors will abort the boot process in either case. Setting this option allows OpenCore to warn the user if the configuration is not as required to achieve an expected higher security level.

12. `ScanPolicy`
    - **Type:** `plist integer`, 32 bit
    - **Failsafe:** `0x10F0103`
    - **Description:** Define operating system detection policy.
    
    This value allows preventing scanning (and booting) untrusted sources based on a bitmask (sum) of a set of flags. As it is not possible to reliably detect every file system or device type, this feature cannot be fully relied upon in open environments, and additional measures are to be applied.
    Third party drivers may introduce additional security (and performance) consideratons following the provided scan policy. The active Scan policy is exposed in the `scan-policy` variable of `4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102` GUID for UEFI Boot Services only.
    
    - `0x00000001` (bit `0`) — `OC_SCAN_FILE_SYSTEM_LOCK`, restricts scanning to only known file systems defined as a part of this policy. File system drivers may not be aware of this policy. Hence, to avoid mounting of undesired file systems, drivers for such file systems should not be loaded. This bit does not affect DMG mounting, which may have any file system. Known file systems are prefixed with `OC_SCAN_ALLOW_FS_`.
    - `0x00000002` (bit `1`) — `OC_SCAN_DEVICE_LOCK`, restricts scanning to only known device types defined as a part of this policy. It is not always possible to detect protocol tunneling, so be aware that on some systems, it may be possible for e.g. USB HDDs to be recognised as SATA instead. Cases like this must be reported. Known device types are prefixed with `OC_SCAN_ALLOW_DEVICE_`.
    - `0x00000100` (bit `8`) — `OC_SCAN_ALLOW_FS_APFS`, allows scanning of APFS file system.
    - `0x00000200` (bit `9`) — `OC_SCAN_ALLOW_FS_HFS`, allows scanning of HFS file system.
    - `0x00000400` (bit `10`) — `OC_SCAN_ALLOW_FS_ESP`, allows scanning of EFI System Partition file system.
    - `0x00000800` (bit `11`) — `OC_SCAN_ALLOW_FS_NTFS`, allows scanning of NTFS (Msft Basic Data) file system.
    - `0x00001000` (bit `12`) — `OC_SCAN_ALLOW_FS_LINUX_ROOT`, allows scanning of Linux Root file systems.
    - `0x00002000` (bit `13`) — `OC_SCAN_ALLOW_FS_LINUX_DATA`, allows scanning of Linux Data file systems.
    - `0x00004000` (bit `14`) — `OC_SCAN_ALLOW_FS_XBOOTLDR`, allows scanning the Extended Boot Loader Partition as defined by the [Boot Loader Specification](https://systemd.io/BOOT_LOADER_SPECIFICATION/).
    - `0x00010000` (bit `16`) — `OC_SCAN_ALLOW_DEVICE_SATA`, allow scanning SATA devices.
    - `0x00020000` (bit `17`) — `OC_SCAN_ALLOW_DEVICE_SASEX`, allow scanning SAS and Mac NVMe devices.
    - `0x00040000` (bit `18`) — `OC_SCAN_ALLOW_DEVICE_SCSI`, allow scanning SCSI devices.
    - `0x00080000` (bit `19`) — `OC_SCAN_ALLOW_DEVICE_NVME`, allow scanning NVMe devices.
    - `0x00100000` (bit `20`) — `OC_SCAN_ALLOW_DEVICE_ATAPI`, allow scanning CD/DVD devices and old SATA.
    - `0x00200000` (bit `21`) — `OC_SCAN_ALLOW_DEVICE_USB`, allow scanning USB devices.
    - `0x00400000` (bit `22`) — `OC_SCAN_ALLOW_DEVICE_FIREWIRE`, allow scanning FireWire devices.
    - `0x00800000` (bit `23`) — `OC_SCAN_ALLOW_DEVICE_SDCARD`, allow scanning card reader devices.
    - `0x01000000` (bit `24`) — `OC_SCAN_ALLOW_DEVICE_PCI`, allow scanning devices directly connected to PCI bus (e.g. VIRTIO).
    
    _Note:_ Given the above description, a value of 0xF0103 is expected to do the following:
    - Permit scanning SATA, SAS, SCSI, and NVMe devices with APFS file systems.
    - Prevent scanning any devices with HFS or FAT32 file systems.
    - Prevent scanning APFS file systems on USB, CD, and FireWire drives.
    
    The combination reads as:
    - `OC_SCAN_FILE_SYSTEM_LOCK`
    - `OC_SCAN_DEVICE_LOCK`
    - `OC_SCAN_ALLOW_FS_APFS`
    - `OC_SCAN_ALLOW_DEVICE_SATA`
    - `OC_SCAN_ALLOW_DEVICE_SASEX`
    - `OC_SCAN_ALLOW_DEVICE_SCSI`
    - `OC_SCAN_ALLOW_DEVICE_NVME`

13. `SecureBootModel`
    - **Type:** `plist string`
    - **Failsafe:** `Default`
    - **Description:** Apple Secure Boot hardware model.
    
    Sets Apple Secure Boot hardware model and policy. Specifying this value defines which operating systems will be bootable. Operating systems shipped before the specified model was released will not boot.
    
    Valid values:
    - `Default` — Matching model for current SMBIOS.
    - `Disabled` — No model, Secure Boot will be disabled.
    - `j137` — `iMacPro1,1` (December 2017). - Minimum `macOS 10.13.2 (17C2111)`
    - `j680` — `MacBookPro15,1` (July 2018). - Minimum `macOS 10.13.6 (17G2112)`
    - `j132` — `MacBookPro15,2` (July 2018). - Minimum `macOS 10.13.6 (17G2112)`
    - `j174` — `Macmini8,1` (October 2018). - Minimum `macOS 10.14 (18A2063)`
    - `j140k` — `MacBookAir8,1` (October 2018). - Minimum `macOS 10.14.1 (18B2084)`
    - `j780` — `MacBookPro15,3` (May 2019). - Minimum `macOS 10.14.5 (18F132)`
    - `j213` — `MacBookPro15,4` (July 2019). - Minimum `macOS 10.14.5 (18F2058)`
    - `j140a` — `MacBookAir8,2` (July 2019). - Minimum `macOS 10.14.5 (18F2058)`
    - `j152f` — `MacBookPro16,1` (November 2019). Minimum `macOS 10.15.1 (19B2093)`
    - `j160` — `MacPro7,1` (December 2019). - Minimum `macOS 10.15.1 (19B88)`
    - `j230k` — `MacBookAir9,1` (March 2020). - Minimum `macOS 10.15.3 (19D2064)`
    - `j214k` — `MacBookPro16,2` (May 2020). - Minimum `macOS 10.15.4 (19E2269)`
    - `j223` — `MacBookPro16,3` (May 2020). - Minimum `macOS 10.15.4 (19E2265)`
    - `j215` — `MacBookPro16,4` (June 2020). - Minimum `macOS 10.15.5 (19F96)`
    - `j185` — `iMac20,1` (August 2020). - Minimum `macOS 10.15.6 (19G2005)`
    - `j185f` — `iMac20,2` (August 2020). - Minimum `macOS 10.15.6 (19G2005)`
    - `x86legacy` — Macs without T2 chip and VMs. - Minimum `macOS 11.0.1 (20B29)`

    **Warning:** Not all Apple Secure Boot models are supported on all hardware configurations.
    Apple Secure Boot appeared in macOS 10.13 on models with T2 chips. Prior to macOS 12 `PlatformInfo` and `SecureBootModel` were independent, allowing Apple Secure Boot can be used with any SMBIOS with and without T2. Starting with macOS 12 `SecureBootModel` must match the SMBIOS Mac model. `Default` model derives the model based on SMBIOS board identifier, either set automatically via the `Generic` section or set manually via the `SMBIOS` section. If there is no board identifier override the model will be derived heuristically from OEM SMBIOS.

    Setting `SecureBootModel` to any valid value but `Disabled` is equivalent to [Medium Security](https://support.apple.com/en-us/HT208330) of Apple Secure Boot. The `ApECID` value must also be specified to achieve `Full Security`. Check `ForceSecureBootScheme` when using Apple Secure Boot on a virtual machine.
    
    Note that enabling Apple Secure Boot is demanding on invalid configurations, faulty macOS installations, and on unsupported setups.

    Things to consider:
    
    1. As with T2 Macs, all unsigned kernel extensions as well as several signed kernel extensions, including NVIDIA Web Drivers, cannot be installed.
    2. The list of cached kernel extensions may be different, resulting in a need to change the list of `Added` or `Forced` kernel extensions. For example, `IO80211Family` cannot be injected in this case.
    3. System volume alterations on operating systems with sealing, such as macOS 11, may result in the operating system being unbootable. Do not try to disable system volume encryption unless Apple Secure Boot is disabled.
    4. Boot failures might occur when the platform requires certain settings, but they have not been enabled because the associated issues were not discovered earlier. Be extra careful with `IgnoreInvalidFlexRatio` or `HashServices`.
    5. Operating systems released before Apple Secure Boot was released (e.g. macOS 10.12 or earlier), will still boot until UEFI Secure Boot is enabled. This is so because Apple Secure Boot treats these as incompatible and they are then handled by the firmware (as Microsoft Windows is).
    6. On older CPUs (e.g. before Sandy Bridge), enabling Apple Secure Boot might cause slightly slower loading (by up to 1 second).
    7. As the `Default` value will increase with time to support the latest major released operating system, it is not recommended to use the `ApECID` and the `Default` settings together.
    8. Installing macOS with Apple Secure Boot enabled is not possible while using HFS+ target volumes. This may include HFS+ formatted drives when no spare APFS drive is available.
    
    The installed operating system may have sometimes outdated Apple Secure Boot manifests on the `Preboot` partition, resulting in boot failures. This is likely to be the case when an `OCB: Apple Secure Boot prohibits this boot entry, enforcing!` message is logged.
    
    When this happens, either reinstall the operating system or copy the manifests (files with `.im4m` extension, such as `boot.efi.j137.im4m`) from `/usr/standalone/i386` to `/Volumes/Preboot/<UUID>/System/Library/CoreServices`. Here, `<UUID>` is the system volume identifier. On HFS+ installations, the manifests should be copied to `/System/Library/CoreServices` on the system volume.

    For more details on how to configure Apple Secure Boot with UEFI Secure Boot, refer to the [UEFI Secure Boot]({%link docs/Troubleshooting/uefi_and_secure_boot.md %}) section.