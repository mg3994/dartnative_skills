---
name: dartnative-system-chrome
description: Guide for controlling system status bar, navigation bar, and device orientation lock in DartNative using SystemChrome.
---

# DartNative SystemChrome

`SystemChrome` provides imperative control over system UI overlays (status bar, navigation bar, and device orientation lock).

---

## 1. Device Orientation Locking

Lock screen orientation using `DeviceOrientation` enums:

```dart
// Lock to portrait
SystemChrome.setPreferredOrientations([DeviceOrientation.portraitUp]);

// Unlock orientation (allow all)
SystemChrome.setPreferredOrientations(DeviceOrientation.values);
```

---

## 2. Status & Navigation Bar Styling (`SystemUiOverlayStyle`)

Configure status/navigation bar colors and icon brightness:

```dart
// Presets
SystemUiOverlayStyle.light // White status bar text/icons (for dark backgrounds)
SystemUiOverlayStyle.dark  // Dark status bar text/icons (for light backgrounds)

// Custom style
SystemChrome.setSystemUIOverlayStyle(
  const SystemUiOverlayStyle(
    statusBarBrightness: Brightness.dark,
    statusBarColor: Colors.transparent,
    statusBarIconBrightness: Brightness.light,
    systemNavigationBarColor: Colors.black,
    systemNavigationBarIconBrightness: Brightness.light,
  ),
);
```

---

## 3. App-Level Default & Route Scope

Assign `defaultStyle` in `main()` so pushed routes inherit the default style automatically when popped:

```dart
void main() {
  SystemChrome.defaultStyle = SystemUiOverlayStyle.dark;
  runApp(const MyApp());
}
```

---

## 4. System UI Modes (`SystemUiMode`)

```dart
SystemChrome.setEnabledSystemUIMode(SystemUiMode.edgeToEdge);      // Normal
SystemChrome.setEnabledSystemUIMode(SystemUiMode.immersiveSticky); // Hide status bar
```

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

class FullscreenVideoPage extends StatefulWidget {
  const FullscreenVideoPage({super.key});

  @override
  State<FullscreenVideoPage> createState() => _FullscreenVideoPageState();
}

class _FullscreenVideoPageState extends State<FullscreenVideoPage> {
  @override
  void initState() {
    super.initState();
    SystemChrome.setPreferredOrientations([
      DeviceOrientation.landscapeLeft,
      DeviceOrientation.landscapeRight,
    ]);
    SystemChrome.setSystemUIOverlayStyle(SystemUiOverlayStyle.light);
  }

  @override
  void dispose() {
    SystemChrome.setPreferredOrientations(DeviceOrientation.values);
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      backgroundColor: Colors.black,
      body: Center(child: Text('Video Player', style: TextStyle(color: Colors.white))),
    );
  }
}
```

---

## Platform Compatibility Matrix

| Feature | iOS Implementation | Android Implementation |
|---|---|---|
| Orientation Lock | `UIInterfaceOrientationMask` | Activity requestedOrientation |
| Status Bar Style | `preferredStatusBarStyle` / `prefersStatusBarHidden` | `WindowInsetsController` |
| Nav Bar Background | Safe area overlay `UIView` | `Window.setNavigationBarColor` |
