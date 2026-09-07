---
name: dartnative-floating-action-button
description: Configure floating action buttons in DartNative using FloatingActionButton. Use when creating circular floating action buttons, setting mini sizes (40x40 pt vs 56x56 pt), customizing background/foreground colors, or displaying action icons over UI content.
---

# DartNative Floating Action Button Guide

`FloatingActionButton` provides a platform-native circular action button that floats above UI content. On iOS, it maps to a circular `UIButton` with a platform drop shadow; on Android, it maps to a native Material FloatingActionButton.

## Basic Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildFAB(BuildContext context) {
  return FloatingActionButton(
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
    onPressed: () {
      print('Floating action button tapped');
    },
    child: const Icon(CupertinoIcons.add),
  );
}
```

## Mini Sizing (`mini`)

Renders a compact 40×40 pt button instead of the standard 56×56 pt size:

```dart
FloatingActionButton(
  mini: true, // 40x40 pt compact sizing
  backgroundColor: Colors.indigo,
  onPressed: () {},
  child: const Icon(CupertinoIcons.pencil),
)
```

## Icon Font Compatibility

The `child` widget is typically an `Icon` using any supported icon set (`CupertinoIcons`, `MaterialSymbolsRounded`, or custom icon fonts). The font glyph is rendered directly on both platform native buttons without requiring SF Symbol string mappings.

```dart
FloatingActionButton(
  backgroundColor: Colors.redAccent,
  foregroundColor: Colors.white,
  onPressed: () {},
  child: const Icon(MaterialSymbolsRounded.favorite),
)
```

## Gotchas

- **Direct Font Glyph Rendering:** The icon child in `FloatingActionButton` renders glyph code points directly onto native platform buttons across iOS and Android without needing extra custom painters or asset bundles.
- **Elevation Shadow:** On iOS, the drop shadow is applied natively via `CALayer` shadow properties (`shadowOpacity`, `shadowRadius`, `shadowOffset`).
- **Import Aliasing:** Always import UI types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
