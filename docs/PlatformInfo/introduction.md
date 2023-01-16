---
layout: default
title: Introduction
parent: PlatformInfo
nav_order: 0
---

# Introduction

Platform information consists of several identification fields generated or filled manually to be compatible with macOS services. The base part of the configuration may be obtained from [`AppleModels`](https://github.com/acidanthera/OpenCorePkg/blob/master/AppleModels), which itself generates a set of interfaces based on a database in [YAML](https://yaml.org/spec/1.2/spec.html) format. These fields are written to three destinations:

- [SMBIOS](https://www.dmtf.org/standards/smbios)
- [Data Hub](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Intel/Protocol/DataHub.h)
- NVRAM

Most of the fields specify the overrides in SMBIOS, and their field names conform to EDK2 [SmBios.h](https://github.com/acidanthera/audk/blob/master/MdePkg/Include/IndustryStandard/SmBios.h) header file. However, several important fields reside in Data Hub and NVRAM. Some of the values can be found in more than one field and/or destination, so there are two ways to control their update process: manual, where all the values are specified (the default), and semi-automatic, where (`Automatic`) only certain values are specified, and later used for system configuration.

The [dmidecode](http://www.nongnu.org/dmidecode) utility can be used to inspect SMBIOS contents and a version with macOS specific enhancements can be downloaded from [Acidanthera/dmidecode](https://github.com/acidanthera/dmidecode/releases).
