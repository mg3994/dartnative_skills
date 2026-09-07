---
name: dartnative-extra-layout
description: Configure specialized layout and utility widgets in DartNative using Wrap, FractionallySizedBox, IntrinsicWidth, IntrinsicHeight, FittedBox, ColoredBox, ClipOval, IgnorePointer, AbsorbPointer, and IndexedStack. Use when wrapping multi-run items, sizing by fraction or intrinsic dimensions, scaling content, solid color filling, circular clipping, disabling touch interaction, or multi-tab state preservation.
---

# DartNative Extra Layout & Utility Widgets Guide

DartNative provides extra specialized layout, clipping, and interaction widgets (`Wrap`, `FractionallySizedBox`, `IntrinsicWidth`, `IntrinsicHeight`, `FittedBox`, `ColoredBox`, `ClipOval`, `IgnorePointer`, `AbsorbPointer`, `IndexedStack`).

## Multi-Run Wrapping (`Wrap`)

Arranges children in multiple horizontal or vertical runs when available space is exhausted (e.g. chip groups or tag clouds):

```dart
import 'package:dartnative/dartnative.dart';

Widget buildTagCloud(List<String> tags) {
  return Wrap(
    spacing: 8.0,    // Horizontal space between adjacent chips
    runSpacing: 4.0, // Vertical space between wrapped runs
    alignment: WrapAlignment.start,
    children: tags.map((tag) => Badge(label: tag, child: const SizedBox.shrink())).toList(),
  );
}
```

## Intrinsic & Fractional Sizing (`FractionallySizedBox`, `IntrinsicWidth`, `IntrinsicHeight`)

Control child dimensions based on parent fractions or intrinsic child bounds:

```dart
// Fractionally sized box (50% of available width)
FractionallySizedBox(
  widthFactor: 0.5,
  alignment: Alignment.center,
  child: const Text('Half Width Panel'),
);

// Forces all children in a Column to equal the width of the widest child
IntrinsicWidth(
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.stretch,
    children: [
      Button(title: 'Short', onPressed: () {}),
      Button(title: 'Much Longer Button Label', onPressed: () {}),
    ],
  ),
);

// Forces children in a Row to match the height of the tallest child
IntrinsicHeight(
  child: Row(
    crossAxisAlignment: CrossAxisAlignment.stretch,
    children: const [
      Text('Short'),
      VerticalDivider(),
      Text('Multi-line\nText Block'),
    ],
  ),
);
```

## Scaling, Solid Color & Oval Clipping (`FittedBox`, `ColoredBox`, `ClipOval`)

```dart
// Scales child content according to BoxFit (e.g., contain or cover)
FittedBox(
  fit: BoxFit.contain,
  child: Image.asset('assets/banner.png'),
);

// Lightweight solid background fill (faster than Container(color: ...))
ColoredBox(
  color: Colors.blueAccent,
  child: const Text('Solid Blue Fill'),
);

// Clips child to an oval (or perfect circle if square) via CALayer cornerRadius
ClipOval(
  child: Image.network('https://example.com/avatar.jpg', width: 48.0, height: 48.0),
);
```

## Touch Interaction Disabling (`IgnorePointer`, `AbsorbPointer`)

Control whether a subtree is visible to touch hit-testing:

```dart
// Disables user interaction on form fields during async loading
IgnorePointer(
  ignoring: isLoading,
  child: const MyFormSubtree(),
);

// Absorbs touch events so they do not pass through to underlying views
AbsorbPointer(
  absorbing: isModalActive,
  child: const BackgroundOverlay(),
);
```

## Tab State Preservation (`IndexedStack`)

Displays only one child at a time while keeping all children mounted in memory. Ideal for fast tab switching without re-inflating views or losing state:

```dart
int _currentTab = 0;

Widget buildTabStack(BuildContext context) {
  return IndexedStack(
    index: _currentTab,
    children: const [
      HomeTabScreen(),
      SearchTabScreen(),
      ProfileTabScreen(),
    ],
  );
}
```

## Gotchas

- **ColoredBox Efficiency:** Prefer `ColoredBox(color: ...)` over `Container(color: ...)` for plain background color fills—it avoids extra `BoxDecoration` overhead.
- **IndexedStack State Preservation:** `IndexedStack` keeps all child views mounted simultaneously. Use it for fast tab switching when children maintain active forms, video playback, or web views.
- **ClipOval Backing:** `ClipOval` is implemented natively via `CALayer.cornerRadius = width / 2` and `masksToBounds = true` on iOS.
- **Import Aliasing:** Always import UI types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
