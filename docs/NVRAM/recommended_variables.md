---
layout: default
title: Recommended Variables
parent: NVRAM
nav_order: 4
---

# Recommended Variables

The following variables are recommended for faster startup or other improvements:

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:BridgeOSHardwareModel`

    Bridge OS hardware model variable used to propagate to IODT bridge-model by `EfiBoot`. Read by `hw.target sysctl`, used by SoftwareUpdateCoreSupport.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:csr-active-config`

    32-bit System Integrity Protection bitmask. Declared in XNU source code in [csr.h](https://opensource.apple.com/source/xnu/xnu-4570.71.2/bsd/sys/csr.h.auto.html).

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:ExtendedFirmwareFeatures`

    Combined `FirmwareFeatures` and `ExtendedFirmwareFeatures`. Present on newer Macs to avoid extra parsing of SMBIOS tables.

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:ExtendedFirmwareFeaturesMask`

    Combined `FirmwareFeaturesMask` and `ExtendedFirmwareFeaturesMask`. Present on newer Macs to avoid extra parsing of SMBIOS tables.

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:HW_BID`

    Hardware `BoardProduct` (e.g. `Mac-35C1E88140C3E6CF`). Not present on real Macs, but used to avoid extra parsing of SMBIOS tables, especially in `boot.efi`.

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:HW_MLB`

    Hardware `BoardSerialNumber`. Override for MLB. Present on newer Macs (2013+ at least).

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:HW_ROM`
    
    Hardware ROM. Override for ROM. Present on newer Macs (2013+ at least).
- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:SSN`
    
    Serial number. Present on newer Macs (2013+ at least).

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:prev-lang:kbd`
    
    ASCII string defining default keyboard layout. Format is `lang-COUNTRY:keyboard`, e.g. `ru-RU:252` for Russian locale and ABC keyboard. Also accepts short forms: `ru:252` or `ru:0` (U.S. keyboard, compatible with 10.9). Full decoded keyboard list from `AppleKeyboardLayouts-L.dat` can be found [here](https://github.com/acidanthera/OpenCorePkg/tree/master/Utilities/AppleKeyboardLayouts). Using non-latin keyboard on 10.14 will not enable ABC keyboard, unlike previous and subsequent macOS versions, and is thus not recommended in case 10.14 is needed.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:security-mode`
    
    ASCII string defining FireWire security mode. Legacy, can be found in IOFireWireFamily source code in [IOFireWireController.cpp](https://opensource.apple.com/source/IOFireWireFamily/IOFireWireFamily-473/IOFireWireFamily.kmodproj/IOFireWireController.cpp.auto.html). It is recommended not to set this variable, which may speedup system startup. Setting to full is equivalent to not setting the variable and `none` disables FireWire security.

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:UIScale`
    
    One-byte data defining `boot.efi` user interface scaling. Should be `01` for normal screens and `02` for HiDPI screens.

- `7C436110-AB2A-4BBB-A880-FE41995C9F82:ForceDisplayRotationInEFI`
    
    32-bit integer defining display rotation. Can be `0` for no rotation or any of `90`, `180`, `270` for matching rotation in degrees.

- `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14:DefaultBackgroundColor`
    
    Four-byte `BGRA` data defining `boot.efi` user interface background colour. Standard colours include `BF BF BF 00` (Light Gray) and `00 00 00 00` (Syrah Black). Other colours may be set at user’s preference.
