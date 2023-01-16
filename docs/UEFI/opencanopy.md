---
layout: default
title: OpenCanopy
nav_order: 4
parent: UEFI
---

# OpenCanopy

OpenCanopy is a graphical OpenCore user interface that runs in `External PickerMode` and relies on [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) `OcBootManagementLib` similar to the builtin text interface.

OpenCanopy requires graphical resources located in `Resources` directory to run. Sample resources (fonts and images) can be found in [OcBinaryData repository](https://github.com/acidanthera/OcBinaryData). Customised icons can be found over the internet (e.g. [here](https://github.com/blackosx/OpenCanopyIcons) or [there](https://applelife.ru/threads/kastomizacija-opencanopy.2945020/)).

OpenCanopy provides full support for `PickerAttributes` and offers a configurable builtin icon set. The chosen icon set may depend on the `DefaultBackgroundColor` variable value. Refer to `PickerVariant` for more details.

Predefined icons are saved in the `PickerVariant`-derived subdirectory of the `\EFI\OC\Resources\Image` directory. A full list of supported icons (in `.icns` format) is provided below. When optional icons are missing, the closest available icon will be used. External entries will use `Ext`-prefixed icon if available (e.g. `OldExtHardDrive`.icns).

_Note:_ In the following all dimensions are normative for the 1x scaling level and shall be scaled accordingly for other levels.

- `Cursor` — Mouse cursor (mandatory, up to 144x144).
- `Selected` — Selected item (mandatory, 144x144).
- `Selector` — Selecting item (mandatory, up to 144x40).
- `SetDefault` — Selecting default (mandatory, up to 144x40; must be same width as Selector).
- `Left` — Scrolling left (mandatory, 40x40).
- `Right` — Scrolling right (mandatory, 40x40).
- `HardDrive` — Generic OS (mandatory, 128x128).
- `Background` — Centred background image.
- `Apple` — Apple OS (128x128).
- `AppleRecv` — Apple Recovery OS (128x128).
- `AppleTM` — Apple Time Machine (128x128).
- `Windows` — Windows (128x128).
- `Other` — Custom entry (see Entries, 128x128).
- `ResetNVRAM` — Reset NVRAM system action or tool (128x128).
- `Shell` — Entry with UEFI Shell name for e.g. OpenShell (128x128).
- `Tool` — Any other tool (128x128).

Predefined labels are saved in the `\EFI\OC\Resources\Label` directory. Each label has `.lbl` or `.l2x` suffix to represent the scaling level. Full list of labels is provided below. All labels are mandatory.

- `EFIBoot` — Generic OS.
- `Apple` — Apple OS.
- `AppleRecv` — Apple Recovery OS.
- `AppleTM` — Apple Time Machine.
- `Windows` — Windows.
- `Other` — Custom entry (see Entries).
- `ResetNVRAM` — Reset NVRAM system action or tool.
- `SIPDisabled` — Toggle SIP tool with SIP disabled.
- `SIPEnabled` — Toggle SIP tool with SIP enabled.
- `Shell` — Entry with UEFI Shell name (e.g. `OpenShell`).
- `Tool` — Any other tool.

_Note:_ All labels must have a height of exactly 12 px. There is no limit for their width.

Label and icon generation can be performed with bundled utilities: `disklabel` and `icnspack`. Font is Helvetica 12 pt
times scale factor.

Font format corresponds to [AngelCode binary BMF](https://www.angelcode.com/products/bmfont). While there are many utilities to generate font files, currently it is recommended to use [dpFontBaker](https://github.com/danpla/dpfontbaker) to generate bitmap font ([using CoreText produces best results](https://github.com/danpla/dpfontbaker/pull/1)) and [fonverter](https://github.com/usr-sse2/fonverter) to export it to binary format.