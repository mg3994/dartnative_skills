---
name: dartnative-container-layout
description: Configure container, box sizing, insets, alignment, and dividers in DartNative using Container, DecoratedBox, Padding, SizedBox, ConstrainedBox, Align, Center, SafeArea, Divider, and VerticalDivider. Use when styling UIView/CALayer properties, constraining box dimensions, positioning aligned content, handling notch/safe area insets, or placing visual dividers.
---

# DartNative Container & Box Layout Guide

DartNative provides box layout and container primitives (`Container`, `DecoratedBox`, `Padding`, `SizedBox`, `ConstrainedBox`, `Align`, `Center`, `SafeArea`, `Divider`, `VerticalDivider`). `Container` backs onto native `UIView` instances with CALayer properties set via FFI.

## Container & Decoration (`Container`, `DecoratedBox`)

`Container` combines sizing, padding, margin, decoration, and alignment onto a native view:

```dart
import 'package:dartnative/dartnative.dart';

Widget buildContainerCard(BuildContext context) {
  return Container(
    width: 300.0,
    height: 150.0,
    padding: const EdgeInsets.all(16.0),
    margin: const EdgeInsets.symmetric(vertical: 8.0),
    alignment: Alignment.centerLeft,
    decoration: BoxDecoration(
      color: Colors.white,
      borderRadius: BorderRadius.circular(12.0),
      boxShadow: const [
        BoxShadow(
          color: Colors.black12,
          blurRadius: 8.0,
          spreadRadius: 2.0,
        ),
      ],
    ),
    child: const Text('Native UIView Card'),
  );
}
```

## Sizing & Alignment (`SizedBox`, `ConstrainedBox`, `Align`, `Center`)

Constrain or align content dimensions:

```dart
// Fixed size box
const SizedBox(width: 100.0, height: 50.0, child: MyWidget());

// Shrink / Expand / Square constructors
const SizedBox.shrink();
const SizedBox.expand();
const SizedBox.square(dimension: 48.0, child: Icon(CupertinoIcons.add));

// Bounded Constraints
ConstrainedBox(
  constraints: const BoxConstraints(
    minWidth: 100.0,
    maxWidth: 250.0,
  ),
  child: const Text('Constrained Text'),
);

// Align / Center
Align(
  alignment: Alignment.topRight,
  child: const Text('Top Right Header'),
);
```

## Insets & Safe Areas (`SafeArea`, `Padding`)

Avoid platform notches, status bars, and home indicators using `SafeArea`:

```dart
// Insets content around notches and home indicators
SafeArea(
  top: true,
  bottom: true,
  child: const MainAppScreen(),
)

// In native bar item slots where an AppBar handles top insets:
SafeArea(
  top: false, // Prevents double top-inset above native AppBars
  child: const ScreenBody(),
)
```

## Dividers (`Divider`, `VerticalDivider`)

Pure-Dart composite dividers that render consistently across all platforms:

```dart
// Horizontal Divider
const Divider(
  height: 20.0,
  thickness: 1.0,
  indent: 16.0,
  endIndent: 16.0,
  color: Colors.grey,
)

// Vertical Divider (inside bounded-height Row)
const VerticalDivider(
  width: 20.0,
  thickness: 1.0,
  indent: 8.0,
  endIndent: 8.0,
  color: Colors.grey,
)
```

## Gotchas

- **Padding in Native Slots:** The standalone `Padding` widget adds no native view of its own. In slots handed directly to native platform containers (such as native list cells or bar items), prefer using `Container(padding: ...)` which creates a real native `UIView` wrapper.
- **`SafeArea` Top Insets with Native `AppBar`:** Native `AppBar` instances supply their own status-bar top insets on iOS 26. When placing a `SafeArea` above or below a native `AppBar`, use `SafeArea(top: false)` to prevent double top-insetting.
- **CALayer Backing:** Properties on `Container.decoration` (border radius, background color, CALayer shadows) are forwarded directly to `CALayer` over FFI for hardware-accelerated rendering.
