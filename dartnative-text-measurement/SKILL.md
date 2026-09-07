---
name: dartnative-text-measurement
description: Guide for text measurement in DartNative using TextPainter and RenderParagraph backed by native FFI NSString boundingRect.
---

# DartNative Text Measurement

`TextPainter` and `RenderParagraph` provide Flutter-compatible text measurement classes backed by native iOS text engines (`NSString.boundingRect(with:options:attributes:)` via FFI).

---

## 1. TextPainter

Calculates text bounding box without mounting a render object tree.

```dart
final tp = TextPainter(
  text: const TextSpan(
    text: 'Hello world',
    style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
  ),
  textDirection: TextDirection.ltr,
);

tp.layout(maxWidth: 300);
final size = tp.size; // Size(width, height)
```

- **`layout({double minWidth = 0.0, double maxWidth = double.infinity})`**: Triggers text measurement.
- **`size` / `width` / `height`**: Getters valid only after `layout()` is invoked.

---

## 2. RenderParagraph

Supports both measurement-only mode and hit-testing on live view instances (`UILabel`).

```dart
// 1. Measurement mode
final rp = RenderParagraph(
  TextSpan(text: 'Hello world', style: const TextStyle(fontSize: 16)),
  textDirection: TextDirection.ltr,
);
rp.layout(const BoxConstraints(maxWidth: 300));
final size = rp.size;

// 2. Character position query on live view
final position = renderParagraph.getPositionForOffset(Offset(10, 5));
print('Character index: ${position.offset}');
```

---

## FFI Performance Note

Each `layout()` call executes a lightweight FFI round-trip to the platform text engine (`NativeBindings.measureText`). Subsequent queries with the same constraints are cached.

---

## Platform Compatibility Matrix

| Class | iOS Backend | Measurement Mechanism |
|---|---|---|
| `TextPainter` | `NSString.boundingRect` | FFI bridge |
| `RenderParagraph` | `UILabel` / TextKit LayoutManager | FFI char offset callback |
