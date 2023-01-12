---
layout: default
title: Generic Terms
parent: PlatformInfo
has_toc: true
---

# Navigation Structure
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Generic Terms

1.1 Generic Terms
- plist — Subset of ASCII Property List format written in XML, also know as XML plist format version 1. Uniform Type Identifier (UTI): com.apple.property-list. Plists consist of plist objects, which are combined to form a hierarchical structure. Due to plist format not being well-defined, all the definitions of this document may only be applied after plist is considered valid by running plutil -lint. External references: https://www.apple.com/DTDs/PropertyList-1.0.dtd, man plutil.
- plist type — plist collections (plist array, plist dictionary, plist key) and primitives (plist string, plist data, plist date, plist boolean, plist integer, plist real).
- plist object — definite realisation of plist type, which may be interpreted as value.
- plist array — array-like collection, conforms to array. Consists of zero or more plist objects.
- plist dictionary — map-like (associative array) collection, conforms to dict. Consists of zero or more plist keys.
- plist key — contains one plist object going by the name of plist key, conforms to key. Consists of printable 7-bit ASCII characters.
- plist string — printable 7-bit ASCII string, conforms to string.
- plist data — base64-encoded blob, conforms to data.
- plist date — ISO-8601 date, conforms to date, unsupported.
- plist boolean — logical state object, which is either true (1) or false (0), conforms to true and false.
- plist integer — possibly signed integer number in base 10, conforms to integer. Fits in 64-bit unsigned integer in two’s complement representation, unless a smaller signed or unsigned integral type is explicitly mentioned in specific plist object description.
- plist real — floating point number, conforms to real, unsupported.
- plist multidata — value cast to data by the implementation. Permits passing plist string, in which case the result is represented by a null-terminated sequence of bytes (C string), plist integer, in which case the result is represented by 32-bit little endian sequence of bytes in two’s complement representation, plist boolean, in which case the value is one byte: 01 for true and 00 for false, and plist data itself. All other types or larger integers invoke undefined behaviour.