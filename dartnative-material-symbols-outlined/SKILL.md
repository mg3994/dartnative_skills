---
name: dartnative-material-symbols-outlined
description: Use Google's Material Symbols Outlined variable font icons in DartNative using MaterialSymbolsOutlined. Use when referencing outlined Google Material symbols, picking icon constants by name, or displaying Material Symbols with the Icon widget.
---

# DartNative Material Symbols Outlined Icons Guide

DartNative provides the `MaterialSymbolsOutlined` icon registry containing over 4,000 icon constants mapped to Google's Material Symbols Outlined variable font (`Material Symbols Outlined`).

## Displaying Outlined Icons (`MaterialSymbolsOutlined`)

Use `MaterialSymbolsOutlined` constants directly with the `Icon` widget:

```dart
import 'package:dartnative/dartnative.dart';

Widget buildOutlinedIconHeader() {
  return Row(
    children: const [
      Icon(MaterialSymbolsOutlined.home, size: 24.0, color: Colors.blue),
      SizedBox(width: 8.0),
      Icon(MaterialSymbolsOutlined.search, size: 24.0, color: Colors.grey),
      SizedBox(width: 8.0),
      Icon(MaterialSymbolsOutlined.settings, size: 24.0, color: Colors.black),
    ],
  );
}
```

## Common Outlined Icon Constants

- **Navigation & Arrows:** `MaterialSymbolsOutlined.arrow_back`, `MaterialSymbolsOutlined.arrow_forward`, `MaterialSymbolsOutlined.chevron_left`, `MaterialSymbolsOutlined.chevron_right`, `MaterialSymbolsOutlined.menu`
- **Actions & Controls:** `MaterialSymbolsOutlined.add`, `MaterialSymbolsOutlined.edit`, `MaterialSymbolsOutlined.delete`, `MaterialSymbolsOutlined.check`, `MaterialSymbolsOutlined.close`, `MaterialSymbolsOutlined.search`
- **User & Account:** `MaterialSymbolsOutlined.account_circle`, `MaterialSymbolsOutlined.person`, `MaterialSymbolsOutlined.group`, `MaterialSymbolsOutlined.manage_accounts`
- **Communication & Media:** `MaterialSymbolsOutlined.mail`, `MaterialSymbolsOutlined.chat`, `MaterialSymbolsOutlined.notifications`, `MaterialSymbolsOutlined.play_arrow`, `MaterialSymbolsOutlined.pause`
- **System & Commerce:** `MaterialSymbolsOutlined.settings`, `MaterialSymbolsOutlined.shopping_cart`, `MaterialSymbolsOutlined.favorite`, `MaterialSymbolsOutlined.thumb_up`

## Platform Font Registration

The `Material Symbols Outlined` font family must be registered with the platform:
- **Android:** Place `MaterialSymbolsOutlined.ttf` in your project's assets directory or package font configuration.
- **iOS:** Include `MaterialSymbolsOutlined.ttf` in your Xcode project target and list it in `Info.plist` under `UIAppFonts`.

## Gotchas

- **Font Family Key:** `MaterialSymbolsOutlined` uses `fontFamily: 'Material Symbols Outlined'`. Ensure the exact string name matches your platform font registration.
- **Icon Aliases & Special Names:** Identifier constants starting with numbers are prefixed with `$` in Dart (e.g., `MaterialSymbolsOutlined.$4k`, `MaterialSymbolsOutlined.$3d_rotation`). Keywords like `class` and `switch` are escaped with trailing underscores (`MaterialSymbolsOutlined.class_`, `MaterialSymbolsOutlined.switch_`).
- **Import Aliasing:** Always import UI types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
