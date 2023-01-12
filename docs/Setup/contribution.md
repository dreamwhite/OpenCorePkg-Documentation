---
layout: default
title: Contribution
parent: Setup
nav_order: 3
---
# Contribution

OpenCore can be compiled as a standard [EDK II](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II) package and requires the [EDK II Stable](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II#stable-tags) package. The currently supported EDK II release is hosted in [acidanthera/audk](https://github.com/acidanthera/audk). Required patches for this package can be found in the `Patches` directory.

When updating the LaTeX documentation (e.g. `Configuration.tex`) please do not rebuild the PDF files till merging to master happens. This avoids unnecessary merge conflicts:
    - External contributors using the pull-request approach should request the maintainers to handle the PDF rebuild in the pull-request message.
    - Internal contributors should rebuild the documentation at merge time in the same or in a separate commit. One can ask another maintainer to rebuild the documentation when lacking the necessary tools in the pull-request message.

The only officially supported toolchain is `XCODE5`. Other toolchains might work but are neither supported nor recommended. Contributions of clean patches are welcome. Please do follow [EDK II C Codestyle](https://github.com/tianocore/tianocore.github.io/wiki/Code-Style-C).

To compile with `XCODE5`, besides [Xcode](https://developer.apple.com/xcode), users should also install [NASM](https://www.nasm.us) and [MTOC](https://github.com/acidanthera/ocbuild/tree/master/external). The latest Xcode version is recommended for use despite the toolchain name. An example command sequence is as follows:

```bash
git clone --depth=1 https://github.com/acidanthera/audk UDK
cd UDK
git submodule update --init --recommend-shallow
git clone --depth=1 https://github.com/acidanthera/OpenCorePkg . ./edksetup.sh
make -C BaseTools
build -a X64 -b RELEASE -t XCODE5 -p OpenCorePkg/OpenCorePkg.dsc
```

For IDE usage Xcode projects are available in the root of the repositories. Another approach could be using [Language Server Protocols](https://microsoft.github.io/language-server-protocol). For example, [Sublime Text](https://www.sublimetext.com) with [LSP for Sublime Text](https://lsp.sublimetext.io) plugin. Add `compile_flags.txt` file with similar content to the UDK root:

```
-I/UefiPackages/MdePkg
-I/UefiPackages/MdePkg/Include
-I/UefiPackages/MdePkg/Include/X64
-I/UefiPackages/MdeModulePkg
-I/UefiPackages/MdeModulePkg/Include
-I/UefiPackages/MdeModulePkg/Include/X64
-I/UefiPackages/OpenCorePkg/Include/AMI
-I/UefiPackages/OpenCorePkg/Include/Acidanthera
-I/UefiPackages/OpenCorePkg/Include/Apple
-I/UefiPackages/OpenCorePkg/Include/Apple/X64
-I/UefiPackages/OpenCorePkg/Include/Duet
-I/UefiPackages/OpenCorePkg/Include/Generic
-I/UefiPackages/OpenCorePkg/Include/Intel
-I/UefiPackages/OpenCorePkg/Include/Microsoft
-I/UefiPackages/OpenCorePkg/Include/Nvidia
-I/UefiPackages/OpenCorePkg/Include/VMware
-I/UefiPackages/OvmfPkg/Include
-I/UefiPackages/ShellPkg/Include
-I/UefiPackages/UefiCpuPkg/Include
-IInclude
-include
/UefiPackages/MdePkg/Include/Uefi.h
-fshort-wchar
-Wall
-Wextra
-Wno-unused-parameter
-Wno-missing-braces
-Wno-missing-field-initializers
-Wno-tautological-compare
-Wno-sign-compare
-Wno-varargs
-Wno-unused-const-variable
-DOC_TARGET_NOOPT=1
-DNO_MSABI_VA_FUNCS=1
```

#### Note: /UefiPackages in the sample file denotes an absolute path.

**Warning**: Tool developers modifying `config.plist` or any other OpenCore files must ensure that their tools check the `opencore-version` NVRAM variable (see the [Debug Properties](/docs/Misc/debug.md) section below) and warn users if the version listed is unsupported or prerelease. The OpenCore configuration may change across releases and such tools shall ensure that they carefully follow this document. Failure to do so may result in such tools being considered to be malware and blocked by any means.
