---
layout: default
title: Configuration Processing
nav_order: 2
parent: Configuration
---

# Introduction
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

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