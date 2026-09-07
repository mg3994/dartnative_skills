---
name: dartnative-slider-menu
description: Guide for SliderMenuContainer in DartNative, providing slide-out push and slide-over reveal drawer layouts.
---

# DartNative SliderMenuContainer

`SliderMenuContainer` is the underlying engine behind `Scaffold.drawer`. It creates a slide-out drawer container that pushes or overlays the main content view to reveal a menu panel underneath.

---

## Key Characteristics & API

```dart
SliderMenuContainer({
  required GlobalKey<SliderMenuContainerState> sliderKey,
  required Widget sliderMain,
  required Widget sliderMenu,
  bool isDraggable = false,
  int animationDuration = 280,
  Curve settleCurve = Curves.easeOut,
  double sliderMenuOpenSize = 270,
  double sliderMenuCloseSize = 0,
  SlideDirection slideDirection = SlideDirection.LEFT_TO_RIGHT,
  DrawerStyle style = DrawerStyle.push,
  SlideOverStyle slideOverStyle = const SlideOverStyle(),
  Color? slideOverBackgroundColor,
  ValueChanged<bool>? onOpenChanged,
  Brightness? brightness,
})
```

---

## Reveal Styles (`DrawerStyle`)

1. **`DrawerStyle.push` (Default)**:
   - The main content slides aside while the drawer slides in from behind.
   - No shadow scrim; tap pushed content or swipe back to close.

2. **`DrawerStyle.slideOver`**:
   - The drawer remains fixed underneath while the main content slides over it.
   - Configurable via `SlideOverStyle` (supports drop shadow along edge and rounded content corners).

---

## Imperative Drawer Control

Use a `GlobalKey<SliderMenuContainerState>` to control the drawer programmatically:

```dart
final drawerKey = GlobalKey<SliderMenuContainerState>();

// Open/close drawer programmatically
drawerKey.currentState?.openDrawer();
drawerKey.currentState?.closeDrawer();
drawerKey.currentState?.toggle();

// Query open status
bool open = drawerKey.currentState?.isDrawerOpen ?? false;
```

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

class CustomDrawerLayout extends StatefulWidget {
  const CustomDrawerLayout({super.key});

  @override
  State<CustomDrawerLayout> createState() => _CustomDrawerLayoutState();
}

class _CustomDrawerLayoutState extends State<CustomDrawerLayout> {
  final _drawerKey = GlobalKey<SliderMenuContainerState>();

  @override
  Widget build(BuildContext context) {
    return SliderMenuContainer(
      sliderKey: _drawerKey,
      style: DrawerStyle.slideOver,
      slideOverStyle: const SlideOverStyle(
        corner: DrawerCorner.rounded,
        radius: 32,
      ),
      sliderMenu: Container(
        color: Colors.indigo,
        child: const Center(child: Text('Menu Panel', style: TextStyle(color: Colors.white))),
      ),
      sliderMain: Scaffold(
        appBar: AppBar(
          title: const Text('Main App'),
          leading: BarButtonItem(
            fontIcon: CupertinoIcons.bars,
            onPressed: () => _drawerKey.currentState?.toggle(),
          ),
        ),
        body: const Center(child: Text('Main Content')),
      ),
    );
  }
}
```

---

## Platform Compatibility Matrix

| Style | Feature | iOS & Android Behavior |
|---|---|---|
| `DrawerStyle.push` | Parallel Slide | Main content & drawer slide together |
| `DrawerStyle.slideOver` | Content Overlay | Content slides over fixed panel with drop shadow |
