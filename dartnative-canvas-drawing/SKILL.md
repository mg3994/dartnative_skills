---
name: dartnative-canvas-drawing
description: Perform low-level canvas painting and text layout in DartNative using CanvasSurface and package:dartnative/canvas.dart as ui. Use when drawing custom shapes, gradients, shaders, custom painters, or laying out paragraphs on a Skia-backed surface.
---

# DartNative Canvas & Drawing Guide

DartNative separates native OS view painting from low-level GPU vector canvas painting. Low-level canvas rendering uses `CanvasSurface` embedded inside a native view tree alongside `package:dartnative/canvas.dart as ui`.

## Importing Canvas APIs

`package:dartnative/canvas.dart` provides `dart:ui`-aligned types (such as `ParagraphBuilder`, `ParagraphStyle`, and `Gradient.linear`) without namespace collisions with `BoxDecoration` gradients in `painting.dart`.

```dart
import 'package:dartnative/dartnative.dart';
import 'package:dartnative/canvas.dart' as ui;
```

## Creating Shaders and Gradients

Use `ui.Gradient` constructors for `Paint.shader`:

```dart
final paint = Paint()
  ..shader = ui.Gradient.linear(
    const Offset(0, 0),
    const Offset(100, 0),
    [Colors.red, Colors.blue],
  );
```

## Multi-Line Paragraph Text Layout

Use `ui.ParagraphBuilder` and `ui.ParagraphConstraints` for manual paragraph formatting and drawing:

```dart
final builder = ui.ParagraphBuilder(ui.ParagraphStyle(
  textAlign: TextAlign.left,
))
  ..pushStyle(ui.TextStyle(
    color: Colors.white,
    fontSize: 16.0,
    fontWeight: FontWeight.bold,
  ))
  ..addText('DartNative Custom Paragraph');

final paragraph = builder.build()
  ..layout(const ui.ParagraphConstraints(width: 250.0));

// Draw onto canvas at specific offset:
canvas.drawParagraph(paragraph, const Offset(20, 20));
```

## Embedding a Skia Island (`CanvasSurface`)

Embed GPU-accelerated rendering inside an otherwise platform-native screen layout:

```dart
Scaffold(
  body: Column(
    children: [
      // Native List (UIScrollView + UILabel)
      Expanded(child: ListView.builder(...)),

      // Native Text Input (UITextField)
      const TextField(),

      // Skia Island — GPU rendered without affecting native Auto Layout
      const CanvasSurface(
        height: 120,
        // Requires dartnative_skia dependency
      ),
    ],
  ),
);
```

## Registering Native Skia Views

When operating at the FFI layer, native Skia views are registered using `skia_view_registry`:

```dart
import 'package:dartnative/dartnative.dart';

// Registering native view pointer with viewId
registerSkiaViewAddress(viewId, rawAddress);

// Unregistering on teardown
unregisterSkiaViewAddress(viewId);
```

## Gotchas

- **Shared Types:** Basic drawing types (`Paint`, `Canvas`, `Path`, `Shader`, `RRect`, `BlendMode`, `Size`, `Offset`, `Rect`, `Color`) are identity-equal whether accessed directly from `package:dartnative/dartnative.dart` or via the `ui.` prefix from `package:dartnative/canvas.dart`.
- **Text Leading Distribution:** `TextLeadingDistribution.proportional` splits line height proportionally to font ascent/descent; `TextLeadingDistribution.even` splits extra leading evenly above and below.
- **Overline Decoration Caveat:** `TextDecoration.overline` is approximated on iOS as an underline with a negative baseline offset; on Android API limits, `overline` is not rendered (whereas `underline` and `lineThrough` are fully supported).
