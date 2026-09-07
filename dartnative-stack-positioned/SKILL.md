---
name: dartnative-stack-positioned
description: Guide for absolute positioning in DartNative using Stack, Positioned, and Positioned.fill with key sizing rules.
---

# DartNative Stack & Positioned

`Stack` and `Positioned` layer child widgets on top of each other using absolute or aligned positioning.

---

## Important Sizing & Positioning Rules (Differs from Flutter)

1. **Non-Positioned Sizing**: The first **NON-positioned** child sizes the `Stack`. A `Stack` containing only `Positioned` or `Positioned.fill` children collapses to zero size inside an unconstrained parent unless given an explicit size or flow child.
2. **Outermost Wrapper Rule**: `Positioned` **MUST** be the outermost wrapper of its child within `Stack.children`. Any wrappers placed above `Positioned` are dropped.
3. **Index Reconciliation**: Children are matched by **INDEX** across rebuilds, not by key.

---

## API Summary

```dart
Stack({
  List<Widget> children = const [],
  AlignmentGeometry alignment = Alignment.topLeft,
  StackFit fit = StackFit.loose,
  Clip clipBehavior = Clip.none,
})

Positioned({
  required Widget child,
  double? left,
  double? top,
  double? right,
  double? bottom,
  double? width,
  double? height,
})

Positioned.fill({required Widget child}) // left/top/right/bottom = 0
```

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildBadgeAvatar() {
  return Stack(
    children: [
      // 1. Flow child (sizes the Stack container)
      const Container(
        width: 60,
        height: 60,
        decoration: BoxDecoration(shape: BoxShape.circle, color: Colors.blue),
      ),
      // 2. Absolutely positioned badge
      Positioned(
        right: 0,
        top: 0,
        child: Container(
          width: 16,
          height: 16,
          decoration: const BoxDecoration(shape: BoxShape.circle, color: Colors.red),
        ),
      ),
    ],
  );
}
```

---

## Platform Compatibility Matrix

| Widget | Sizing Behavior | Reconciliation |
|---|---|---|
| `Stack` | Sized by 1st flow child | Index-matched |
| `Positioned` | Outermost child wrapper required | Contributes 0 to Stack size |
