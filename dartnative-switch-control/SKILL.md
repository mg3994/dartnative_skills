---
name: dartnative-switch-control
description: Guide for Switch in DartNative, mapping to UISwitch on iOS and MaterialSwitch on Android.
---

# DartNative Switch & AndroidSwitchStyle

`Switch` maps to native toggle controls (`UISwitch` on iOS and `MaterialSwitch` on Android).

---

## Key Characteristics & API

```dart
Switch({
  required bool value,
  ValueChanged<bool>? onChanged,
  Color? activeThumbColor,    // Android only
  Color? activeTrackColor,    // Maps to onTintColor on iOS
  Color? inactiveThumbColor,  // Single thumb tint on iOS
  Color? inactiveTrackColor,  // Android only
  AndroidSwitchStyle? android,
})
```

---

## Platform Color Mapping Limits

Due to native OS control constraints:

- **iOS `UISwitch`**:
  - `activeTrackColor` $\rightarrow$ `onTintColor` (ON state track fill).
  - `inactiveThumbColor` $\rightarrow$ `thumbTintColor` (single thumb tint).
  - `activeThumbColor` & `inactiveTrackColor` are no-ops on iOS.
- **Android `MaterialSwitch`**:
  - Full support for all 4 color parameters via track/thumb `ColorStateList`s.

---

## Material 3 Extras (`AndroidSwitchStyle`)

Configures Android Material 3 switch features:

```dart
Switch(
  value: _isOn,
  onChanged: (v) => setState(() => _isOn = v),
  android: const AndroidSwitchStyle(
    checkIcon: true,        // Render checkmark icon on thumb when ON
    thumbIconSize: 18,
  ),
)
```

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildSettingsToggle(bool isEnabled, ValueChanged<bool> onToggle) {
  return Switch(
    value: isEnabled,
    onChanged: onToggle,
    activeTrackColor: const Color(0xFF34C759),
    inactiveThumbColor: Colors.white,
  );
}
```

---

## Platform Compatibility Matrix

| Feature | iOS `UISwitch` | Android `MaterialSwitch` |
|---|---|---|
| Active Track Color | Supported (`onTintColor`) | Supported |
| Inactive Track Color | Not supported by iOS API | Supported |
| Thumb Check Icon | Not supported on iOS | Supported via `android:` |
