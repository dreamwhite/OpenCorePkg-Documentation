---
layout: default
title: Kernel
nav_order: 7
parent: Kernel
---

# Introduction

This section allows the application of different kinds of kernelspace modifications on Apple Kernel ([XNU](https://opensource.apple.com/source/xnu)). The modifications currently provide driver (kext) injection, kernel and driver patching, and driver blocking.

Kernel and kext changes apply with the following effective order:

- `Block` is processed.
- `Emulate` and `Quirks` are processed.
- `Patch` is processed.
- `Add` and `Force` are processed.