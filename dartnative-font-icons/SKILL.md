---
name: dartnative-font-icons
description: Use bundled font icons in DartNative using CupertinoIcons and MaterialSymbolsRounded. Use when selecting icon glyphs, referencing Cupertino/Material symbol constants, or displaying font icons without manual asset configuration.
---

# DartNative Font Icons Guide

DartNative includes built-in font icon constants (`CupertinoIcons` and `MaterialSymbolsRounded`). The underlying TTF fonts ship directly with the `dartnative` package and are registered with the platform automatically—no project-level pubspec asset declarations required.

## Displaying Cupertino Font Icons (`CupertinoIcons`)

Use `CupertinoIcons` constants directly inside an `Icon` widget:

```dart
import 'package:dartnative/dartnative.dart';

Widget buildIconHeader() {
  return Row(
    children: const [
      Icon(CupertinoIcons.house_fill, size: 24.0, color: Colors.blue),
      SizedBox(width: 8.0),
      Icon(CupertinoIcons.heart_fill, size: 24.0, color: Colors.red),
      SizedBox(width: 8.0),
      Icon(CupertinoIcons.gear, size: 24.0, color: Colors.grey),
    ],
  );
}
```

## Common CupertinoIcon Glyphs

- **Navigation & Arrows:** `CupertinoIcons.chevron_left`, `CupertinoIcons.chevron_right`, `CupertinoIcons.arrow_left`, `CupertinoIcons.arrow_right`, `CupertinoIcons.arrow_clockwise`
- **Actions:** `CupertinoIcons.pencil`, `CupertinoIcons.trash`, `CupertinoIcons.plus`, `CupertinoIcons.minus`, `CupertinoIcons.xmark`, `CupertinoIcons.checkmark`, `CupertinoIcons.search`
- **Status & Media:** `CupertinoIcons.play_fill`, `CupertinoIcons.pause_fill`, `CupertinoIcons.bell_fill`, `CupertinoIcons.star_fill`, `CupertinoIcons.camera_fill`, `CupertinoIcons.phone_fill`
- **Objects & Interfaces:** `CupertinoIcons.person_fill`, `CupertinoIcons.folder_fill`, `CupertinoIcons.envelope_fill`, `CupertinoIcons.house_fill`, `CupertinoIcons.lock_fill`

## Asset Font Registration & Subsetting

The `CupertinoIcons` and `MaterialSymbolsRounded` TTF font assets are bundled with the `dartnative` package (`fontPackage: 'dartnative'`):

```yaml
flutter:
  fonts:
    - family: Material Symbols Rounded
      fonts:
        - asset: assets/fonts/MaterialSymbolsRounded.ttf
    - family: CupertinoIcons
      fonts:
        - asset: assets/fonts/CupertinoIcons.ttf
```

## Gotchas

- **Zero Asset Boilerplate:** `CupertinoIcons` constants configure `fontFamily: 'CupertinoIcons'` and `fontPackage: 'dartnative'` automatically—apps never need to declare font paths in their own `pubspec.yaml`.
- **Static vs Variable Fonts:** Font files bundled with DartNative are static instances (e.g. `FILL=0 GRAD=0 opsz=24 wght=400`). Variable font TTFs are avoided because the engine's font-subset tool can corrupt `gvar` data, causing glyphs to rasterize empty on CoreText release builds.
- **Icon Aliases:** For backward compatibility, common aliases exist on `CupertinoIcons`:
  - `CupertinoIcons.fullscreen` → `arrow_up_left_arrow_down_right`
  - `CupertinoIcons.fullscreen_exit` → `arrow_down_right_arrow_up_left`
  - `CupertinoIcons.repeat_icon` → `repeat`
