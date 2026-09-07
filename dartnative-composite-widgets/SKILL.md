---
name: dartnative-composite-widgets
description: Use pure-Dart composite UI components in DartNative including Card, ListTile, Badge, and CircleAvatar. Use when displaying elevated rounded cards, table view cell list rows, badge notification overlays, or user avatar circles with initials or network images.
---

# DartNative Pure-Dart Composite Widgets Guide

DartNative includes pure-Dart composite widgets (`Card`, `ListTile`, `Badge`, `CircleAvatar`) that compose underlying primitive containers, decorations, stacks, and gesture detectors. Because they are pure Dart compositions built via `StatelessWidget.build()`, they require no extra native FFI binding overhead while matching platform-native styling.

## Cards (`Card`)

Surfaces content inside a container with rounded corners, background color, elevation shadow, and inner padding:

```dart
import 'package:dartnative/dartnative.dart';

Widget buildCard(BuildContext context) {
  return Card(
    elevation: 2.0,
    borderRadius: 12.0,
    color: Colors.white,
    padding: const EdgeInsets.all(16.0),
    child: Column(
      children: [
        Text('Card Title', style: Theme.of(context).textTheme.titleLarge),
        const SizedBox(height: 8.0),
        const Text('Card body content goes here.'),
      ],
    ),
  );
}
```

## List Rows (`ListTile`)

Creates fixed-height list tile rows matching `UITableViewCell` (on iOS) and Material list tiles (on Android):

```dart
ListTile(
  leading: const CircleAvatar(
    radius: 18.0,
    backgroundColor: Colors.blue,
    child: Text('JD', style: TextStyle(color: Colors.white)),
  ),
  title: const Text('John Doe'),
  subtitle: const Text('Software Engineer'),
  trailing: const Icon(CupertinoIcons.chevron_right),
  onTap: () {
    print('Tile tapped');
  },
)
```

## Notification Badges (`Badge`)

Positions a red notification badge overlay at the top-right corner of a child widget (e.g., an icon or avatar). Automatically caps counts above 99 to `"99+"`:

```dart
// Numeric badge
Badge(
  count: 5, // Displays '5' (or '99+' when > 99)
  badgeColor: Colors.red,
  child: const Icon(CupertinoIcons.bell, size: 28.0),
)

// Text label badge
Badge(
  label: 'NEW',
  isLarge: true,
  child: const Icon(CupertinoIcons.mail, size: 28.0),
)
```

## Avatars (`CircleAvatar`)

Displays a circular profile avatar with background image URL or fallback initials text:

```dart
// Image avatar
const CircleAvatar(
  radius: 24.0,
  backgroundImage: 'https://example.com/avatar.jpg',
)

// Initials text avatar
const CircleAvatar(
  radius: 20.0,
  backgroundColor: Colors.indigo,
  child: Text('JD', style: TextStyle(color: Colors.white)),
)
```

## Gotchas

- **Pure-Dart Efficiency:** Composite widgets (`Card`, `ListTile`, `Badge`, `CircleAvatar`) are pure Dart compositions—they build upon `Container`, `Stack`, and `GestureDetector` without adding FFI binding layers.
- **Badge Count Capping:** `Badge` automatically formats `count` integers greater than 99 as the string `"99+"`.
- **Card Shape Overrides:** When `Card.shape` is provided as a `RoundedRectangleBorder`, its internal `borderRadius` takes precedence over the `borderRadius` parameter.
