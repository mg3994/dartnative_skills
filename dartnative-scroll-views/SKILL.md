---
name: dartnative-scroll-views
description: Guide for scrollable views in DartNative including SingleChildScrollView, ListView, ListView.builder, and ScrollPhysics.
---

# DartNative Scroll Views & ListView

`SingleChildScrollView` and `ListView` provide scrollable view containers backed by `UIScrollView` on iOS and native ScrollViews on Android.

---

## 1. SingleChildScrollView

Wraps a single child widget in a scrollable view.

```dart
SingleChildScrollView({
  required Widget child,
  Axis scrollDirection = Axis.vertical,
  bool reverse = false,
  EdgeInsetsGeometry? padding,
  ScrollController? controller,
  ScrollPhysics? physics,
  bool showScrollBar = false,
})
```

---

## 2. ListView & ListView.builder

Scrollable linear list of widgets.

```dart
// Standard ListView (eager children list)
ListView({
  List<Widget> children = const [],
  ScrollController? controller,
  ListController? listController,
  EdgeInsetsGeometry? padding,
  Axis scrollDirection = Axis.vertical,
  bool reverse = false,
  bool shrinkWrap = false,
  double? itemExtent,
  ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
  ScrollPhysics? physics,
  bool showScrollBar = false,
})

// Builder ListView (Flutter-compatibility API)
ListView.builder({
  required int? itemCount,
  required IndexedWidgetBuilder itemBuilder,
  // ...other options
})
```

> **Performance Note**: `ListView.builder` in DartNative is a compatibility API where items are built eagerly without cell recycling. For long or dynamic lists requiring cell recycling, use `FastList`.

---

## 3. ScrollPhysics

Controls scroll boundary physics behavior:

- **`BouncingScrollPhysics`**: iOS-style elastic bounce at boundaries.
- **`ClampingScrollPhysics`**: Android-style edge glow / clamp.
- **`NeverScrollableScrollPhysics`**: Disables scrolling entirely.
- **`AlwaysScrollableScrollPhysics`**: Enables scrolling even if content fits container.

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildScrollableContent() {
  return SingleChildScrollView(
    physics: const BouncingScrollPhysics(),
    child: Column(
      children: const [
        Text('Header'),
        SizedBox(height: 200),
        Text('Footer'),
      ],
    ),
  );
}
```

---

## Platform Compatibility Matrix

| Component | iOS Implementation | Android Implementation |
|---|---|---|
| `SingleChildScrollView` | `UIScrollView` | `ScrollView` / `HorizontalScrollView` |
| `ListView` | `UIScrollView` | `ScrollView` |
