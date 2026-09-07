---
name: dartnative-custom-paint
description: Perform low-level 2D canvas painting and custom graphic drawing in DartNative using CustomPaint, CustomPainter, Paint, Path, RRect, CanvasImage, and package:dartnative/canvas.dart as ui. Use when writing CustomPainter implementations, recording display lists into BinaryCanvasRecorder, applying shaders/gradients, or comparing Native CoreGraphics/Android Canvas vs Skia backends.
---

# DartNative CustomPaint & Canvas Drawing Guide

DartNative provides custom 2D canvas drawing through the `CustomPaint` widget and `CustomPainter` abstract class, backed by two distinct execution engines:

1. **Native Display List Backend (Default - No Skia):** The painter records drawing commands into a binary display list (`BinaryCanvasRecorder`) that is shipped to the native side in a single FFI mutation (`SetCustomPaintData`) and replayed directly against Core Graphics (iOS) or `android.graphics.Canvas` (Android).
2. **Skia Backend (`dartnative_skia`):** Opt-in GPU Skia Graphite rendering via `CanvasSurface`. Used for SkSL runtime shaders, exotic blend modes, and mesh rendering.

## Importing Canvas APIs

Custom canvas drawing types are exposed via `package:dartnative/canvas.dart as ui` to mirror Flutter's `dart:ui` layout without naming collisions with `painting.dart` box decorations:

```dart
import 'package:dartnative/dartnative.dart';
import 'package:dartnative/canvas.dart' as ui;
```

## Creating a `CustomPainter`

Implement a `CustomPainter` subclass and override `paint(Canvas canvas, Size size)` and `shouldRepaint()`:

```dart
class MyChartPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    // 1. Configure Paint with solid color or Gradient shader
    final paint = Paint()
      ..style = PaintingStyle.fill
      ..shader = ui.Gradient.linear(
        Offset.zero,
        Offset(size.width, size.height),
        [Colors.blue, Colors.purple],
      );

    // 2. Draw basic shapes
    canvas.drawRect(Rect.fromLTWH(0, 0, size.width, size.height), paint);

    // 3. Draw Path with Stroke
    final strokePaint = Paint()
      ..style = PaintingStyle.stroke
      ..strokeWidth = 3.0
      ..color = Colors.white;

    final path = Path()
      ..moveTo(0, size.height / 2)
      ..lineTo(size.width / 2, 0)
      ..lineTo(size.width, size.height / 2)
      ..close();

    canvas.drawPath(path, strokePaint);

    // 4. Single-line direct text drawing (DartNative convenience)
    canvas.drawText(
      'Chart Title',
      const Offset(20, 20),
      fontSize: 18.0,
      color: Colors.white,
      fontWeight: 700,
    );
  }

  @override
  bool shouldRepaint(covariant MyChartPainter oldDelegate) => false;
}
```

## Using `CustomPaint` Widget

Embed custom painters inside widget trees:

```dart
CustomPaint(
  size: Size(MediaQuery.sizeOf(context).width, 220.0),
  painter: MyChartPainter(),
  child: const Center(
    child: Text('Overlay above custom canvas'),
  ),
)
```

## Binary Canvas Recorder (`BinaryCanvasRecorder`)

On the default native backend, `CustomPaint` uses `BinaryCanvasRecorder` to encode operations into a `Uint8List` buffer:

```dart
final recorder = BinaryCanvasRecorder();
painter.paint(recorder, Size(300, 200));

// Finish recording and ship Uint8List bytes over FFI mutation
final Uint8List displayListBytes = recorder.finish();
reconciler.enqueueMutation(SetCustomPaintData(viewId, displayListBytes));
```

## Gotchas

- **Concrete Size Requirement:** On the default native `CustomPaint` backend, the painter runs at mount time against the widget's `size`. Non-finite dimensions (`double.infinity`) propagate as infinity through painter math and land as off-screen coordinates. Always supply concrete dimensions via `MediaQuery.sizeOf(context).width` or explicit parent constraints.
- **Gradient Strokes:** Setting a `ui.Gradient` shader on a `Paint` with `style = PaintingStyle.stroke` falls back to the paint's solid `color` (gradient strokes are not supported on the native backend).
- **Import Aliasing:** Import `package:dartnative/canvas.dart as ui` (not `dart:ui`) to access `ui.Gradient`, `ui.TileMode`, `ui.ParagraphBuilder`, and `ui.ParagraphStyle`.
