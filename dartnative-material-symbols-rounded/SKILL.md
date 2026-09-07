---
name: dartnative-material-symbols-rounded
description: Detailed guide for using Google Material Symbols Rounded icons in DartNative, including platform font registration, tree shaking, and static font requirements.
---

# DartNative Material Symbols Rounded Icons

`MaterialSymbolsRounded` provides access to Google's Material Symbols Rounded icon set in DartNative apps. Each icon constant maps to a specific Unicode glyph in the Material Symbols Rounded font family.

---

## Key Characteristics & Setup

- **Font Family Name:** `Material Symbols Rounded`
- **Font Package:** `dartnative` (icons set `fontPackage: 'dartnative'` internally)
- **Annotation:** `@staticIconProvider` on `abstract class MaterialSymbolsRounded`
- **Widget Usage:** Standard `Icon` widget (e.g., `Icon(MaterialSymbolsRounded.home, size: 24)`)

---

## Basic Usage Example

```dart
import 'package:dartnative/dartnative.dart';

Widget buildIconRow() {
  return Row(
    children: const [
      Icon(MaterialSymbolsRounded.home, size: 24, color: Colors.blue),
      SizedBox(width: 8),
      Icon(MaterialSymbolsRounded.settings, size: 24),
      SizedBox(width: 8),
      Icon(MaterialSymbolsRounded.search, size: 24),
    ],
  );
}
```

---

## Font Subsetting & Tree Shaking

In DartNative, Material Symbols font icons undergo automatic tree shaking per app build:

1. **Automatic Font Manifest Entry:** Consumers receive the font family automatically registered as `packages/dartnative/Material Symbols Rounded`.
2. **Icon Tree Shaker:** The zero build pipeline scans constant icon usages (`const MaterialSymbolsRounded.*` $\rightarrow$ `const_finder` $\rightarrow$ `font-subset`).
3. **Size Optimization:** Only the glyphs actually referenced by the app are bundled into the release binary, reducing font bundle size from ~1.7 MB down to a few KB.
4. **Package Matching:** `IconData` constants specify `fontPackage: 'dartnative'` to match the subsetter's lookup path.

---

## Static Font Requirement & Instancing

> **Critical Gotcha:** You **MUST** use a static instanced font TTF rather than a variable font TTF.

### Why Variable Fonts Fail
The Flutter engine's `font-subset` tool corrupts variable font `gvar` table data during subsetting. Glyphs keep their `cmap` entries but rasterize **EMPTY** on iOS CoreText in release builds, resulting in missing icon graphics.

### Generating the Static Font
Material Symbols Rounded is converted from Google's variable font into a static instance at `FILL=0 GRAD=0 opsz=24 wght=400` using `fonttools`:

```bash
fonttools varLib.instancer FILL=0 GRAD=0 opsz=24 wght=400 MaterialSymbolsRounded-Variable.ttf -o MaterialSymbolsRounded.ttf
```

---

## Platform Font Registration

### Android
Place the instanced `MaterialSymbolsRounded.ttf` file in your app's `assets/fonts/` directory and reference it in `pubspec.yaml`:

```yaml
flutter:
  fonts:
    - family: Material Symbols Rounded
      fonts:
        - asset: assets/fonts/MaterialSymbolsRounded.ttf
```

### iOS
1. Drag `MaterialSymbolsRounded.ttf` into your Xcode project target.
2. Ensure the font file is listed under **Build Phases $\rightarrow$ Copy Bundle Resources**.
3. In `Info.plist`, declare the font under `UIAppFonts`:
   ```xml
   <key>UIAppFonts</key>
   <array>
       <string>MaterialSymbolsRounded.ttf</string>
   </array>
   ```

---

## Platform Compatibility Matrix

| Platform | Font Registration Mechanism | Release Subsetting |
|---|---|---|
| **iOS** | Xcode `Info.plist` (`UIAppFonts`) + CoreText | Supported (requires static font) |
| **Android** | `assets/fonts/` + `pubspec.yaml` | Supported |
