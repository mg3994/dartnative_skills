---
name: dartnative-segmented-control
description: Guide for SegmentedControl in DartNative, mapping to UISegmentedControl on iOS.
---

# DartNative SegmentedControl

`SegmentedControl` presents a horizontal set of mutually-exclusive options, mapping directly to `UISegmentedControl` on iOS.

---

## Key Characteristics & API

```dart
SegmentedControl({
  required List<String> segments,      // List of segment title strings
  int selectedIndex = 0,               // Index of currently selected segment
  ValueChanged<int>? onValueChanged,   // Fired when user selects a segment
  Color? indicatorColor,              // Maps to selectedSegmentTintColor on iOS
  Color? backgroundColor,             // Background tint
  TextStyle? labelFontStyle,           // Base text style for segments
  TextStyle? selectedLabelFontStyle,   // Text style for selected segment
})
```

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildSegmentedToggle(int currentTab, ValueChanged<int> onTabChanged) {
  return SegmentedControl(
    segments: const ['Grid', 'Masonry', 'List'],
    selectedIndex: currentTab,
    onValueChanged: onTabChanged,
    indicatorColor: Colors.blue,
    backgroundColor: Colors.grey.shade200,
    labelFontStyle: const TextStyle(fontSize: 14, color: Colors.black54),
    selectedLabelFontStyle: const TextStyle(fontSize: 14, fontWeight: FontWeight.bold, color: Colors.white),
  );
}
```

---

## Platform Compatibility Matrix

| Widget | iOS Implementation | Android / Fallback |
|---|---|---|
| `SegmentedControl` | `UISegmentedControl` | Material Button Toggle Group |
