---
name: dartnative-icons-buttons
description: Display native icons and interactive icon buttons in DartNative using Icon, IconData, IconButton, and ButtonStyle. Use when displaying Material Symbols or Cupertino font glyphs, creating touch-friendly icon buttons, or configuring button visual styles for Flutter API compatibility.
---

# DartNative Icons & Icon Buttons Guide

DartNative provides native-backed icon display (`Icon`) and touch-friendly icon buttons (`IconButton`). Icons render font glyphs directly on native platform image views (`UIImageView` / Android views) with automatic SF Symbols fallback on iOS.

## Icon Display (`Icon`)

Renders icon glyphs using `IconData` from icon sets like `CupertinoIcons`, `MaterialSymbolsRounded`, or custom icon fonts:

```dart
import 'package:dartnative/dartnative.dart';

// Cupertino Icon
const Icon(
  CupertinoIcons.heart_fill,
  size: 28.0,
  color: Colors.red,
)

// Material Symbols Rounded Icon
const Icon(
  MaterialSymbolsRounded.check_circle,
  size: 24.0,
  color: Colors.green,
)
```

## Interactive Icon Buttons (`IconButton`)

`IconButton` composes a `GestureDetector` around a padded, centered icon widget, maintaining full Flutter API compatibility without requiring complex native button subclass bindings:

```dart
IconButton(
  iconSize: 24.0,
  padding: const EdgeInsets.all(8.0),
  color: Colors.blue,
  icon: const Icon(CupertinoIcons.share),
  onPressed: () {
    print('Share tapped');
  },
)
```

## Button Styling (`ButtonStyle`)

For Flutter API compatibility when configuring custom button styles or wrapper themes:

```dart
const style = ButtonStyle(
  foregroundColor: Colors.white,
  backgroundColor: Colors.black,
  padding: EdgeInsets.symmetric(horizontal: 16.0, vertical: 8.0),
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.all(Radius.circular(8.0)),
  ),
  elevation: 2.0,
);
```

## Gotchas

- **Glyph Rendering:** Native icons load font glyphs (codePoint and fontFamily) directly onto platform image views without requiring custom Skia canvas painters or SF Symbol mappings.
- **Touch Target Padding:** `IconButton` defaults to `padding: EdgeInsets.all(8.0)` and `iconSize: 24.0` (38px+ total target) to ensure comfortable native touch target sizes.
- **Color Override Rules:** Passing `color` or `iconSize` directly to `IconButton` overrides any internal `color` or `size` set on its child `Icon` widget.
