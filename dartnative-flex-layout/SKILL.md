---
name: dartnative-flex-layout
description: Arrange widgets in 1D linear layouts in DartNative using Column, Row, Flex, Flexible, Expanded, and Spacer. Use when laying out vertical or horizontal flex structures backed by native UIStackView/LinearLayout, allocating proportional flex space, or aligning children.
---

# DartNative Flex Layout Guide

DartNative provides flex layout widgets (`Column`, `Row`, `Flex`, `Flexible`, `Expanded`, `Spacer`) that map directly to native flex layout components (`UIStackView` / Auto Layout on iOS and `LinearLayout` / Flexbox on Android).

## Column & Row

- **`Column`**: Arranges child widgets vertically (`Axis.vertical`).
- **`Row`**: Arranges child widgets horizontally (`Axis.horizontal`).

```dart
import 'package:dartnative/dartnative.dart';

Widget buildUserHeader(BuildContext context) {
  return Row(
    mainAxisAlignment: MainAxisAlignment.spaceBetween,
    crossAxisAlignment: CrossAxisAlignment.center,
    children: [
      const CircleAvatar(radius: 20.0, child: Text('JD')),
      const SizedBox(width: 12.0),
      const Expanded(
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          mainAxisSize: MainAxisSize.min,
          children: [
            Text('John Doe', style: TextStyle(fontWeight: FontWeight.bold)),
            Text('Software Engineer', style: TextStyle(color: Colors.grey)),
          ],
        ),
      ),
      const Icon(CupertinoIcons.chevron_right),
    ],
  );
}
```

## Flex Sizing & Spacing (`Expanded`, `Flexible`, `Spacer`)

Control how children expand to fill available space along the main axis:

- **`Expanded`**: Forces a child to expand and fill available main-axis space (`FlexFit.tight`).
- **`Flexible`**: Allows a child to expand up to available space while retaining its natural size (`FlexFit.loose`).
- **`Spacer`**: An empty space consumer taking proportional flex space (`flex: 1` by default).

```dart
Row(
  children: [
    const Text('Leading Title'),
    const Spacer(), // Pushes trailing button to the end edge
    Button(title: 'Action', onPressed: () {}),
  ],
)
```

## Alignment Options

- **`MainAxisAlignment`**: Controls child distribution along the main axis (`start`, `end`, `center`, `spaceBetween`, `spaceAround`, `spaceEvenly`).
- **`CrossAxisAlignment`**: Controls child alignment along the cross axis (`start`, `end`, `center`, `stretch`, `baseline`).
- **`MainAxisSize`**: `MainAxisSize.max` (expands to fill parent) vs `MainAxisSize.min` (shrinks to fit children).

## Gotchas

- **Native UIStackView / LinearLayout Backing:** `Column` and `Row` translate directly to native stack layout primitives rather than custom Flutter flex render objects.
- **Unbounded Constraints:** Do not place `Expanded` or `Spacer` inside unconstrained scrollable viewports (such as a vertical `FastList` or `SingleChildScrollView`) without explicit height bounds.
- **UI Aliasing:** Always import types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
