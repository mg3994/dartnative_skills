---
name: dartnative-material-symbols-sharp
description: Detailed guide for using Google Material Symbols Sharp icons in DartNative, including font package configuration, platform font registration, tree shaking, and static font requirements.
---

# DartNative Material Symbols Sharp Icons

`MaterialSymbolsSharp` provides access to Google's Material Symbols Sharp icon set in DartNative apps. Each icon constant maps to a specific Unicode glyph in the Material Symbols Sharp font family.

---

## Key Characteristics & Setup

- **Font Family Name:** `Material Symbols Sharp`
- **Font Package:** `dartnative_symbols_sharp`
- **Annotation:** `@staticIconProvider` on `abstract class MaterialSymbolsSharp`
- **Widget Usage:** Standard `Icon` widget (e.g., `Icon(MaterialSymbolsSharp.home, size: 24)`)

---

## Basic Usage Example

```dart
import 'package:dartnative/dartnative.dart';

Widget buildSharpIconRow() {
  return Row(
    children: const [
      Icon(MaterialSymbolsSharp.home, size: 24, color: Colors.indigo),
      SizedBox(width: 8),
      Icon(MaterialSymbolsSharp.settings, size: 24),
      SizedBox(width: 8),
      Icon(MaterialSymbolsSharp.search, size: 24),
    ],
  );
}
```

---

## Font Subsetting & Tree Shaking

In DartNative, Material Symbols font icons undergo automatic tree shaking per app build:

1. **Font Package Identifier:** `IconData` constants set `fontPackage: 'dartnative_symbols_sharp'` to direct font assets to the dedicated sharp symbols package.
2. **Icon Tree Shaker:** The zero build pipeline scans constant icon usages (`const MaterialSymbolsSharp.*` $\rightarrow$ `const_finder` $\rightarrow$ `font-subset`).
3. **Size Optimization:** Only the glyphs actually referenced by the app are bundled into the release binary, reducing font bundle size from ~1.7 MB down to a few KB.

---

## Static Font Requirement & Instancing

> **Critical Gotcha:** You **MUST** use a static instanced font TTF rather than a variable font TTF.

### Why Variable Fonts Fail
The Flutter engine's `font-subset` tool corrupts variable font `gvar` table data during subsetting. Glyphs keep their `cmap` entries but rasterize **EMPTY** on iOS CoreText in release builds, resulting in missing icon graphics.

### Generating the Static Font
Material Symbols Sharp is converted from Google's variable font into a static instance at `FILL=0 GRAD=0 opsz=24 wght=400` using `fonttools`:

```bash
fonttools varLib.instancer FILL=0 GRAD=0 opsz=24 wght=400 MaterialSymbolsSharp-Variable.ttf -o MaterialSymbolsSharp.ttf
```

---

## Platform Font Registration

### Android
Place the instanced `MaterialSymbolsSharp.ttf` file in your app's `assets/fonts/` directory and reference it in `pubspec.yaml`:

```yaml
flutter:
  fonts:
    - family: Material Symbols Sharp
      fonts:
        - asset: assets/fonts/MaterialSymbolsSharp.ttf
```

### iOS
1. Drag `MaterialSymbolsSharp.ttf` into your Xcode project target.
2. Ensure the font file is listed under **Build Phases $\rightarrow$ Copy Bundle Resources**.
3. In `Info.plist`, declare the font under `UIAppFonts`:
   ```xml
   <key>UIAppFonts</key>
   <array>
       <string>MaterialSymbolsSharp.ttf</string>
   </array>
   ```

---

## Platform Compatibility Matrix

| Platform | Font Registration Mechanism | Release Subsetting |
|---|---|---|
| **iOS** | Xcode `Info.plist` (`UIAppFonts`) + CoreText | Supported (requires static font) |
| **Android** | `assets/fonts/` + `pubspec.yaml` | Supported |
