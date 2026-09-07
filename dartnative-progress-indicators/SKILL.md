---
name: dartnative-progress-indicators
description: Detailed guide for using CircularProgressIndicator and LinearProgressIndicator in DartNative with native UIActivityIndicatorView, UIProgressView, and Material 3 wavy styles.
---

# DartNative Progress Indicators

`CircularProgressIndicator` and `LinearProgressIndicator` map to native platform progress views (`UIActivityIndicatorView` and `UIProgressView` on iOS; Material ProgressBars on Android).

---

## 1. CircularProgressIndicator

Renders an indeterminate animated spinner or a determinate progress ring.

```dart
CircularProgressIndicator({
  Color? color,
  Animation<Color>? valueColor,
  double? strokeWidth,
  double? value, // null = indeterminate spinner; 0.0–1.0 = ring
  Color? backgroundColor,
  AndroidProgressIndicatorStyle? android,
})
```

- **Indeterminate (`value == null`)**: Autostarts platform spinner (`UIActivityIndicatorView` on iOS).
- **Determinate (`value` 0.0–1.0)**: Arc ring (`CAShapeLayer` on iOS).
- **`valueColor`**: Flutter-compat `Animation<Color>` taking precedence over `color`.

---

## 2. LinearProgressIndicator

Renders a horizontal progress bar.

```dart
LinearProgressIndicator({
  double? value, // 0.0–1.0 for progress; null for indeterminate bar
  Color? color,
  Color? backgroundColor,
  double? minHeight,
  Animation<Color>? valueColor,
  AndroidProgressIndicatorStyle? android,
})
```

- Maps to `UIProgressView` on iOS.
- **`color`**: Sets `progressTintColor`.
- **`backgroundColor`**: Sets `trackTintColor`.

---

## 3. AndroidProgressIndicatorStyle

Configures Material 3 Android attributes (`com.google.android.material.progressindicator`), including wavy progress lines.

```dart
AndroidProgressIndicatorStyle({
  bool wavy = false,
  double? waveAmplitude,
  double? wavelength,
  double? waveSpeed,
  double? trackGap,
  double? stopIndicatorSize,
})
```

> **Note**: Ignored on iOS where indicators maintain standard native HIG appearance.

---

## Basic Usage Example

```dart
import 'package:dartnative/dartnative.dart';

Widget buildProgressSection() {
  return Column(
    children: [
      // Indeterminate spinner
      const CircularProgressIndicator(),
      const SizedBox(height: 16),

      // Determinate 70% blue progress bar
      const LinearProgressIndicator(
        value: 0.7,
        color: Colors.blue,
        backgroundColor: Colors.grey,
      ),
      const SizedBox(height: 16),

      // Material 3 Wavy bar on Android
      const LinearProgressIndicator(
        value: 0.5,
        android: AndroidProgressIndicatorStyle(
          wavy: true,
          waveAmplitude: 4.0,
        ),
      ),
    ],
  );
}
```

---

## Platform Compatibility Matrix

| Widget | iOS Native View | Android View |
|---|---|---|
| `CircularProgressIndicator` | `UIActivityIndicatorView` / `CAShapeLayer` | Material `CircularProgressIndicator` |
| `LinearProgressIndicator` | `UIProgressView` | Material 3 `LinearProgressIndicator` |
