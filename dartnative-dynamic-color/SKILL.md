---
name: dartnative-dynamic-color
description: Access device wallpaper-derived Material You dynamic colors in DartNative using DynamicColor, CorePalette, and TonalPalette. Use when reading Android 12+ wallpaper color schemes, obtaining primary/secondary/tertiary/neutral tonal palettes, or applying M3 dynamic color schemes with fallbacks.
---

# DartNative Dynamic Color (Material You) Guide

DartNative provides access to Material You wallpaper-derived dynamic palettes on Android 12+ via `DynamicColor`, `CorePalette`, and `TonalPalette`, matching Android Compose Material3 role mappings.

## Obtaining Dynamic Color Schemes (`DynamicColor.colorScheme`)

Read the device's dynamic `ColorScheme` for light or dark brightness modes, providing a fallback for platforms without wallpaper palette access (iOS, Android < 12):

```dart
import 'package:dartnative/dartnative.dart';

Widget buildDynamicThemeApp(BuildContext context) {
  // Read device palette (returns null on iOS or Android < 12)
  final dynamicLight = DynamicColor.colorScheme(brightness: Brightness.light);
  final dynamicDark = DynamicColor.colorScheme(brightness: Brightness.dark);

  // Always supply a fallback theme
  final lightTheme = ThemeData(
    colorScheme: dynamicLight ?? ColorScheme.light(),
  );

  final darkTheme = ThemeData(
    colorScheme: dynamicDark ?? ColorScheme.dark(),
  );

  return App(
    theme: lightTheme,
    darkTheme: darkTheme,
    themeMode: ThemeMode.system,
    home: const HomeScreen(),
  );
}
```

## Raw Core Palettes (`DynamicColor.corePalette` & `TonalPalette`)

For custom branding or advanced M3 surface roles, query the raw five `CorePalette` tonal palettes (`primary`, `secondary`, `tertiary`, `neutral`, `neutralVariant`) across tones 0 (black) to 100 (white):

```dart
final core = DynamicColor.corePalette();

if (core != null) {
  // Access specific M3 tones (0-100 scale)
  final primaryTone40 = core.primary.tone(40);
  final surfaceTone90 = core.neutral.tone(90);
  final variantTone80 = core.neutralVariant.tone(80);

  print('Device Primary Tone 40: $primaryTone40');
}
```

## Platform Availability Matrix

| Platform | `DynamicColor.corePalette()` / `colorScheme()` | Behavior |
|---|---|---|
| **Android 12+ (API 31+)** | `CorePalette` / `ColorScheme` | Reads system wallpaper palette (cached per app cold start). |
| **Android < 12 (API < 31)** | `null` | Returns `null` — use seed color fallback scheme. |
| **iOS** | `null` | Returns `null` — adaptive-first philosophy (never fakes platform dynamic colors). |

## Gotchas

- **Fallback Always Required:** Always use null-coalescing (`scheme ?? myFallbackScheme`) when calling `DynamicColor.colorScheme()` or `corePalette()` because iOS and Android < 12 return `null`.
- **Cached Read:** Palette values are queried once from native system resources at launch and cached. Changes to wallpaper take effect on the next app launch.
- **Tone Scale:** `TonalPalette.tone(int tone)` accepts values from 0 to 100. Non-exact tones automatically interpolate linearly between the nearest sampled system tones.
