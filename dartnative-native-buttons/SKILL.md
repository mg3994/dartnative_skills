---
name: dartnative-native-buttons
description: Configure unified native platform buttons in DartNative using Button, ButtonVariant, and IconButton. Use when creating standard buttons, filled/elevated/tinted/bordered/plain buttons, iOS 26 Liquid Glass buttons, setting custom shapes (CircleBorder, StadiumBorder), custom elevation/shadows, or mapping from Flutter button widgets.
---

# DartNative Native Buttons Guide

DartNative follows a unified widget philosophy: **One widget per concept, mapped to the best native control on each platform**. Rather than creating multiple button widget classes, DartNative provides a single `Button` widget backed directly by native `UIButton` (on iOS) and `MaterialButton` / `android.widget.Button` (on Android).

## Flutter Button Mapping Matrix

| Flutter Widget | DartNative Equivalent |
|---|---|
| `ElevatedButton` | `Button(variant: ButtonVariant.filled, elevation: 2)` |
| `FilledButton` | `Button(variant: ButtonVariant.filled)` |
| `FilledButton.tonal` | `Button(variant: ButtonVariant.tinted)` |
| `OutlinedButton` | `Button(variant: ButtonVariant.bordered)` |
| `TextButton` | `Button(variant: ButtonVariant.plain)` |
| `CupertinoButton` | `Button()` (Native iOS default) |
| `MaterialButton` | `Button(color: ..., shape: CircleBorder(), padding: ...)` |

## Button Variants (`ButtonVariant`)

Customize button appearance using the `variant` property:

- **`filled`**: Solid filled background (`UIButton.Configuration.filled` / `MaterialButton` solid tint).
- **`tinted`**: Semi-transparent tinted background (`UIButton.Configuration.tinted` / M3 tonal).
- **`gray`**: Neutral gray background (`UIButton.Configuration.gray`).
- **`bordered`**: Outlined border style (`UIButton.Configuration.bordered`).
- **`plain`**: Text/icon only without background (`UIButton.Configuration.plain`).
- **`glass`**: iOS 26 Liquid Glass capsule (`UIButton.Configuration.glass()`).
- **`clearGlass`**: iOS 26 clear Liquid Glass capsule (`UIButton.Configuration.clearGlass()`).
- **`prominentGlass`**: iOS 26 prominent tinted Liquid Glass capsule.
- **`prominentClearGlass`**: iOS 26 prominent clear Liquid Glass capsule.

```dart
import 'package:dartnative/dartnative.dart';

// Filled Elevated Button
Button(
  title: 'Submit Order',
  variant: ButtonVariant.filled,
  elevation: 2.0,
  onPressed: () {
    print('Order submitted');
  },
)

// Liquid Glass Capsule Button (iOS 26)
Button(
  title: 'Glass Action',
  variant: ButtonVariant.glass,
  color: Colors.blue,
  automaticTint: false, // Ensures solid color accuracy on glass vibrancy
  onPressed: () {},
)
```

## Custom Shapes, Padding, and Asset Images

Customize button geometry and icons using native properties:

```dart
// Circular Icon Button (replacing MaterialButton)
Button(
  color: const Color.fromRGBO(0, 0, 0, 0.4),
  padding: const EdgeInsets.all(14.0),
  shape: const CircleBorder(),
  onPressed: () {},
  child: const Icon(MaterialSymbolsRounded.pause, size: 34.0, color: Colors.white),
)

// Button with Leading Asset / SF Symbol / Drawable Image
Button(
  title: 'Continue with Apple',
  imageAsset: 'apple-logo', // Asset path, SF Symbol ('apple.logo'), or Android drawable
  imageSize: 20.0,
  variant: ButtonVariant.bordered,
  shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(8.0)),
  onPressed: () {},
)
```

## Gotchas

- **Native Control Backing:** `Button` maps to platform native primitives (`UIButton` / `MaterialButton`) rather than Flutter-emulated canvas paths or custom painters.
- **`child` Slot Support:** When `child` is a `Text` widget, its string and font properties are extracted to native button title attributes. When `child` is an `Icon` widget, its glyph codePoint and font family are loaded onto the native icon slot. Other widget types in `child` will log a warning (use `imageAsset` for images).
- **Glass Automatic Tinting (`automaticTint`):** On iOS 26 frosted glass variants (`glass`, `prominentGlass`), set `automaticTint: false` (default) to force a solid baked fill of `foregroundColor` so vibrancy does not wash out custom brand colors.
