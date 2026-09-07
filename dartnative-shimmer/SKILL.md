---
name: dartnative-shimmer
description: Detailed guide for Shimmer loading effects in DartNative using Shimmer.fromColors and ShimmerStyle.
---

# DartNative Shimmer

`Shimmer` provides a native skeleton loading shimmer effect as a drop-in replacement for the pub.dev `shimmer` package.

---

## Key Characteristics & API

```dart
Shimmer.fromColors({
  Key? key,
  required Widget child,
  required Color baseColor,
  required Color highlightColor,
  Duration period = const Duration(milliseconds: 1500), // Animation cycle duration
  bool enabled = true,                                   // Set false to freeze animation after data loads
  int loop = 0,                                          // 0 = infinite loop; positive = repeat N times
  ShimmerStyle style = ShimmerStyle.wide,               // Highlight band style
})
```

---

## Highlight Band Styles (`ShimmerStyle`)

- **`ShimmerStyle.wide` (Default)**: Wide, soft bell-curve gradient sweep across the view with seamless looping.
- **`ShimmerStyle.narrow`**: Crisp, 30% width gradient stripe matching `shimmer` pub.dev package v3.0.0.

---

## Native Performance

On iOS, `Shimmer` is implemented natively using `CAGradientLayer` and `CABasicAnimation`. This provides smooth 60/120 FPS hardware-accelerated shimmer animations without Impeller or ShaderMask GPU overhead.

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildSkeletonLoader() {
  return Shimmer.fromColors(
    baseColor: const Color(0xFFE0E0E0),
    highlightColor: const Color(0xFFF5F5F5),
    style: ShimmerStyle.wide,
    child: Column(
      children: [
        Container(width: double.infinity, height: 100, color: Colors.white),
        const SizedBox(height: 8),
        Container(width: 200, height: 20, color: Colors.white),
      ],
    ),
  );
}
```

---

## Platform Compatibility Matrix

| Widget | iOS Native Implementation | Android Implementation |
|---|---|---|
| `Shimmer` | `CAGradientLayer` + `CABasicAnimation` | Native Shimmer Mask |
