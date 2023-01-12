---
layout: default
title: Configuration
nav_order: 2
has_children: true
---

# Introduction
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}
## Configuration Terms

- `OC config` — OpenCore Configuration file in `plist` format named `config.plist`. It provides an extensible way to configure OpenCore and is structured to be separated into multiple named sections situated under the root `plist dictionary`. These sections may have `plist array` or `plist dictionary` types and are described in corresponding sections of this document.
- `valid key` — `plist key` object of `OC config` described in this document or its future revisions. Besides explicitly described `valid keys`, keys starting with the `#` symbol (e.g. `#Hello`) are also considered `valid keys` and while they behave as comments, effectively discarding their values, they are still required to be valid `plist objects`. All other `plist keys` are not valid, and their presence results in `undefined behaviour`.
- `valid value` — valid `plist object` of `OC config` described in this document that matches all the additional requirements in specific `plist object` descriptions if any.
- `invalid value` — valid `plist object` of `OC config` described in this document that is of other `plist type`, does not conform to additional requirements found in specific `plist object` descriptions (e.g. value range), or missing from the corresponding collection. `Invalid values` are read with or without an error message as any possible value of this `plist object` in an undetermined manner (i.e. the values may not be same across the reboots). Whilst reading an `invalid value` is equivalent to reading certain defined `valid values`, applying incompatible values to the host system may result in `undefined behaviour`.
- `optional value` — `valid value` of `OC config` described in this document that reads in a certain defined manner provided in specific `plist object` description (instead of `invalid value`) when not present in `OC config`. All other cases of `invalid value` do still apply. Unless explicitly marked as `optional value`, any other value is required to be present and reads to `invalid value` if missing.
- `fatal behaviour` — behaviour leading to boot termination. Implementations shall prevent the boot process from continuing until the host system is restarted. It is permitted, but not required, to execute cold reboots or to show warning messages in such cases.
- `undefined behaviour` — behaviour not prescribed by this document. Implementations may take any measures including, but not limited to, measures associated with `fatal behaviour`, assumptions of any state or value, or disregarding any associated states or values. This is however subject to such measures not negatively impacting upon system integrity.

## Configuration Processing

The `OC config` file is guaranteed to be processed at least once if found. Subject to the OpenCore bootstrapping mechanism, the presence of multiple `OC config` files may lead to the reading of any of them. It is permissible for no `OC Config` file to be present on disk. In such cases, if the implementation does not abort the boot process, all values shall follow the rules of `invalid values` and `optional values`.

The `OC config` file has restrictions on size, nesting levels, and number of keys:
- The `OC config` file size shall not exceed `32 MBs`.
- The `OC config` file shall not have more than `32` nesting levels.
- The `OC config` file may have up to `32,768` XML nodes within each `plist object`.
    – One `plist dictionary` item is counted as a pair of nodes

Reading malformed `OC config` files results in `undefined behaviour`. Examples of malformed OC` config` files include
the following:

- `OC config` files that do not conform to `DTD PLIST 1.0`.
- `OC config` files with unsupported or non-conformant `plist objects` found in this document.
- `OC config` files violating restrictions on size, nesting levels, and number of keys.

It is recommended, but not required, to abort loading malformed `OC config` files and to continue as if an `OC config` file is not present. For forward compatibility, it is recommended, but not required, for the implementation to warn about the use of `invalid values`.

The recommended approach to interpreting invalid values is to conform to the following convention where applicable:

| `Type` | `Value` |
|---|---|
| `plist string` | Empty string (`<string></string>`) |
| `plist data` | Empty data (`<data></data>`) |
| `plist integer` | 0 (`<integer>0</integer>`) |
| `plist boolean` | False (`<false/>`) |
| `plist tristate` | False (`<false/>`) |

## Configuration Structure

The `OC config` file is separated into subsections, as described in separate sections of this document, and is designed so as to attempt not to enable anything by default as well as to provide kill switches via an `Enable` property for `plist dict` entries that represent optional plugins and similar.

The file is structured to group related elements in subsections as follows:
- `Add` provides support for data addition. Existing data will not be overridden, and needs to be handled separately with Delete if necessary.
- `Delete` provides support for data removal.
- `Patch` provides support for data modification.
- `Quirks` provides support for specific workarounds.

Root configuration entries consist of the following:

- `ACPI`
- `Booter`
- `DeviceProperties`
- `Kernel`
- `Misc`
- `NVRAM`
- `PlatformInfo`
- `UEFI`

Basic validation of an `OC config` file is possible using the `ocvalidate` utility. Please note that the version of `ocvalidate` used must match the OpenCore release and that notwithstanding this, it may not detect all configuration issues present in an `OC config` file.

Note: To maintain system integrity, properties typically have predefined values even when such predefined values are not specified in the `OC config` file. However, all properties must be explicitly specified in the `OC config` file and this behaviour should not be relied on.