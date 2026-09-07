---
name: dartnative-app-navigation
description: Configure app initialization, navigation, routes, Hero animations, native sheets, dialogs, media queries, and themes in DartNative apps. Use when setting up runApp, registering routes, pushing PageRoute transitions, showing native modal sheets or bottom overlays, reading MediaQuery, applying ThemeData, or triggering HapticFeedback.
---

# DartNative App Framework & Navigation Guide

DartNative provides an app-level framework for screen routing, native sheet/overlay presentation, theme management, and device metric access.

## App Setup & Startup

Initialize platform bindings and mount the root application widget:

```dart
import 'package:dartnative/dartnative.dart';

void main() {
  // 1. Register routes for hot restart navigation replay
  registerRoutes({
    '/': (_) => const HomeScreen(),
    '/settings': (_) => const SettingsScreen(),
  });

  // 2. Mount root app
  runApp(const App(
    title: 'My DartNative App',
    home: HomeScreen(),
  ));
}
```

## Navigation & Page Transitions

Push and pop screens using `Navigator` and `PageRoute`.

```dart
// Standard navigation with slideFromRight transition (default)
Navigator.push(
  context,
  PageRoute(
    builder: (_) => const SettingsScreen(),
    settings: '/settings', // Route name required for hot restart replay
    transition: RouteTransition.slideFromRight,
  ),
);

// Modal transition (slideFromBottom)
Navigator.push(
  context,
  PageRoute(
    builder: (_) => const ModalScreen(),
    transition: RouteTransition.slideFromBottom,
    duration: const Duration(milliseconds: 300),
  ),
);

// Apple-style Zoom transition (morphs from source Hero tag)
Navigator.push(
  context,
  PageRoute(
    builder: (_) => const DetailScreen(),
    transition: RouteTransition.zoom,
    zoomSourceTag: 'card-123',
  ),
);

// Pop current route
Navigator.pop(context, 'result_data');
```

## Shared Element Transitions (`Hero`)

Animate widgets smoothly across routes using matching `Hero` tags:

```dart
// Source Route Screen
Hero(
  tag: 'card-123',
  child: Image.asset('assets/cover.png'),
)

// Destination Route Screen
Hero(
  tag: 'card-123',
  child: Image.asset('assets/cover.png'),
)
```

## Native Sheets, Overlays & Dialogs

### 1. Detent Modal Sheet (`showModalSheet`)
iOS `UISheetPresentationController` detent sheet with spring physics and drag handle:

```dart
final result = await showModalSheet<String>(
  context: context,
  detent: SheetDetent.large, // medium, large, adaptive, fitContent
  showDragHandle: true,
  header: SheetHeader(
    title: 'Edit Profile',
    close: BarButtonItem(
      title: 'Close',
      onPressed: () => Navigator.pop(context),
    ),
    action: BarButtonItem(
      title: 'Done',
      prominent: true,
      onPressed: () => Navigator.pop(context, 'saved'),
    ),
  ),
  builder: (context) => const ProfileEditorForm(),
);
```

### 2. Native Bottom Sheet & Centered Dialog
VC-contained bottom sheet or centered dialog overlays:

```dart
// Content-sized bottom overlay
await showModalBottomSheet(
  context: context,
  cornerRadius: 15.0,
  dimOpacity: 0.4,
  builder: (_) => const QuickActionSheet(),
);

// Native centered dialog
await showDialog(
  context: context,
  cornerRadius: 15.0,
  builder: (_) => const ConfirmationDialog(),
);
```

### 3. Keyboard Overlay (`showKeyboardOverlay`)
Presents a Dart UI surface above the keyboard without hiding the soft input keyboard:

```dart
await showKeyboardOverlay(
  context: context,
  builder: (_) => const AttachmentPickerPanel(),
);
```

## Theme & Media Query

Access system metrics, insets, and themes using `MediaQuery` and `Theme`:

```dart
Widget build(BuildContext context) {
  final screenSize = MediaQuery.sizeOf(context);
  final keyboardInsets = MediaQuery.viewInsetsOf(context);
  final theme = Theme.of(context);

  return Container(
    color: theme.colorScheme.surface,
    padding: EdgeInsets.only(bottom: keyboardInsets.bottom),
    child: Text(
      'Screen width: ${screenSize.width}',
      style: theme.textTheme.bodyLarge,
    ),
  );
}
```

## Haptic Feedback & System Actions

```dart
// Trigger platform haptic feedback
HapticFeedback.lightImpact();
HapticFeedback.mediumImpact();
HapticFeedback.heavyImpact();
HapticFeedback.selectionClick();

// Pop app or move task to background
SystemNavigator.pop();
```

## Gotchas

- **Route Names for Hot Restart:** Always specify `settings: '/routeName'` when pushing routes if hot-restart navigation replay is required.
- **Hero Constraints:** Only one `Hero` per tag is permitted per active route. Pair with `RouteTransition.none` for clean morph transitions without page slide collisions.
- **Keyboard Insets:** `MediaQuery.viewInsetsOf(context)` updates automatically when the on-screen keyboard appears without requiring a `Scaffold`.
