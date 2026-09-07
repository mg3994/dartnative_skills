---
name: dartnative-icon-tree-shaking
description: Optimize icon font asset sizes in DartNative using IconData, staticIconProvider, and font subsetting. Use when defining custom icon fonts, configuring fontPackage, marking icon registry classes, or understanding how const_finder subsets packaged TTF font assets per app.
---

# DartNative Icon Data & Tree Shaking Guide

DartNative mirrors Flutter's `IconData` and `@staticIconProvider` structure, enabling the zero-build toolchain's icon tree shaker to analyze constant icon usages (`const_finder`) and subset packaged TTF fonts PER APP. This reduces icon font asset sizes from ~1.7 MB down to a few KB.

## Defining Icon Constants (`IconData`)

`IconData` specifies the Unicode code point, `fontFamily`, and optional `fontPackage`:

```dart
import 'package:dartnative/dartnative.dart';

// Package font icon data constant
const IconData kCustomHomeIcon = IconData(
  0xF101,
  fontFamily: 'CustomIcons',
  fontPackage: 'my_icon_package',
);
```

## Marking Icon Registries (`@staticIconProvider`)

Mark custom icon registry classes with `@staticIconProvider` (or `staticIconProvider`) so `const_finder` identifies them during icon tree shaking:

```dart
import 'package:dartnative/dartnative.dart';

@staticIconProvider
abstract class MyCustomIcons {
  static const IconData dashboard = IconData(
    0xF201,
    fontFamily: 'MyCustomIcons',
    fontPackage: 'dartnative',
  );

  static const IconData settings = IconData(
    0xF202,
    fontFamily: 'MyCustomIcons',
    fontPackage: 'dartnative',
  );
}
```

## How Font Subsetting Works

1. **`fontPackage` Key:** When `fontPackage` is set (e.g. `fontPackage: 'dartnative'`), the effective font manifest key resolves to `packages/<fontPackage>/<fontFamily>`.
2. **`const_finder` Analysis:** During release builds, `const_finder` scans all `const IconData(...)` invocations in the app's widget tree and extracts `(codePoint, fontFamily, fontPackage)`.
3. **`font-subset` Generation:** The build tool instances a subset TTF containing only used glyphs via `fonttools varLib.instancer`.

## Gotchas & Font Constraints

- **MUST Be Static Fonts:** Never use variable font TTFs with `font-subset`. The engine's font-subset tool corrupts variable fonts' `gvar` table data—glyphs retain their `cmap` entries but rasterize completely EMPTY on CoreText (iOS release builds). Always instance variable fonts at target axes (e.g. `FILL=0 GRAD=0 opsz=24 wght=400`) into static TTFs before bundling.
- **Const Invocations:** Tree shaking only detects `const IconData(...)` members. Dynamically constructed `IconData(codePoint)` instances (non-const at runtime) bypass `const_finder` analysis.
- **Import Aliasing:** Always import UI types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
