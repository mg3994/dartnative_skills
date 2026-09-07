---
name: dartnative-flutter-migration
description: Migrate Flutter projects to DartNative by replacing Flutter imports with package:dartnative/flutter_compat.dart and mapping widgets across Tier 1 native shims, Tier 2 Skia surfaces, and Tier 3 stubs. Use when migrating existing Flutter code, replacing import statements, handling custom painting with CanvasSurface, or debugging tier incompatibility errors.
---

# DartNative Flutter Migration Guide

Migrate Flutter applications to DartNative for native OS rendering (UIKit on iOS / Android views) without changing core Dart UI code structures.

## Import Swapping Strategy

Replace standard Flutter imports with DartNative compatibility imports:

```dart
// Before:
import 'package:flutter/material.dart';
import 'package:flutter/cupertino.dart';

// After (Tier 1 + Tier 2 stubs):
import 'package:dartnative/flutter_compat.dart';

// Or (Full Native API without Flutter compat layer):
import 'package:dartnative/dartnative.dart';
```

## Widget Support Tiers

### Tier 1 — Native OS Widgets (Default)
Tier 1 widgets compile directly to platform-native components (UIKit/Android views).

* **Text & Input:** `Text` → `UILabel`, `TextField` → `UITextField`/`UITextView`
* **Scrolling:** `ListView`, `SingleChildScrollView` → `UIScrollView`
* **Layout & Navigation:** `Scaffold` → `UIViewController`, `AppBar` → `UINavigationBar`, `Column`, `Row`, `Expanded` → `UIStackView` + Auto Layout
* **Containers & Decoration:** `Container`, `ColoredBox` → `UIView` with `CALayer` props
* **Gestures & Focus:** `GestureDetector`, `Listener` → `UIGestureRecognizer` / touch callbacks, `Focus`, `FocusNode` → `UIResponder`
* **Buttons:** `ElevatedButton`, `TextButton`, `OutlinedButton`, `FilledButton`, `InkWell` → `UIButton`
* **Animations:** `AnimationController`, `Tween`, `CurvedAnimation` → `CADisplayLink`-driven native transform/opacity
* **Modals & Overlays:** `Drawer`, `BottomSheet`, `showModalBottomSheet`, `AlertDialog` → Native alerts / sheets

### Tier 2 — Skia-Backed Custom Painting
Requires `dartnative_skia` package dependency.

Widgets that require GPU-accelerated low-level canvas painting are handled via `CanvasSurface`:
* `CustomPaint` → Wrap inside `CanvasSurface(painter: ...)`
* `Hero`, `ShaderMask`, `Flow` → Require `CanvasSurface` island

```dart
// Before (Flutter CustomPaint):
CustomPaint(
  painter: MyPainter(),
  child: const Text('Overlay'),
)

// After (DartNative CanvasSurface):
CanvasSurface(
  width: 300,
  height: 200,
  // Embed GPU rendering island inside native view tree
)
```

### Tier 3 — Unsupported Web / Low-Level APIs
Web-specific foundation APIs and direct engine-dependent stubs throw `UnimplementedError` or `UnsupportedError` at runtime in `flutter_compat.dart`. Replace these with native DartNative equivalents or custom native plugins.

## Migration Checklist & Workflow

1. Update `pubspec.yaml`:
   - Replace or complement `flutter` dependencies with `dartnative`.
   - Note: DartNative uses standard `ffi: ^2.1.4` and `meta: ^1.17.0` directly on `dart:ui`/FFI without binding consumer apps to the Flutter SDK lock context.
2. Replace import lines across `.dart` files with `import 'package:dartnative/flutter_compat.dart';`.
3. Check debug assertions for `_Tier2Stub` widgets and refactor them into `CanvasSurface` islands.
4. Verify custom button styles (`ElevatedButton.styleFrom` etc.) map to native `Button` variants or custom `Container` + `GestureDetector`.

## Gotchas

- **Font Asset Subsetting:** DartNative subsets Material Symbols Rounded per app using `const_finder` and `font-subset`. Material symbols must be static fonts (not variable TTFs) to avoid empty rendering artifacts on iOS CoreText release builds.
- **Color Opacity / Channels:** In DartNative `Color.a`, `Color.r`, `Color.g`, `Color.b` return `0-255` integers (unlike Flutter's `0.0-1.0` doubles). Divide by `255.0` when providing values to normalized shader math.
- **Borders:** Only uniform borders render natively. Non-uniform border sides are simplified to the `top` side applied across all 4 sides. Use a thin absolutely-positioned `Container` to draw single edge dividers.
