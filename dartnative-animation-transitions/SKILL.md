---
name: dartnative-animation-transitions
description: Build explicit and implicit animations in DartNative using AnimationController, CurvedAnimation, Tween, AnimatedBuilder, and transition widgets. Use when creating spring physics fling animations, ticker-driven controls, AnimatedContainer, AnimatedSwitcher, Transform.perspective, or chained TweenSequence interpolations.
---

# DartNative Animations & Transitions Guide

DartNative drives UI animations directly on platform timing loops (`CADisplayLink` on iOS and Choreographer on Android) without requiring Skia, enabling low-overhead native transform and opacity changes.

## Explicit Animations (`AnimationController`)

Drive explicit timeline or physical spring animations with `AnimationController` and `SingleTickerProviderStateMixin`.

```dart
class MyAnimatedScreen extends StatefulWidget {
  const MyAnimatedScreen({super.key});

  @override
  State<MyAnimatedScreen> createState() => _MyAnimatedScreenState();
}

class _MyAnimatedScreenState extends State<MyAnimatedScreen>
    with SingleTickerProviderStateMixin {
  late final AnimationController _controller;
  late final Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      vsync: this,
      duration: const Duration(milliseconds: 300),
    );

    _animation = CurvedAnimation(
      parent: _controller,
      curve: Curves.easeInOut,
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  void _triggerFling() {
    // Physical spring fling simulation (ideal for native drawers/panels)
    _controller.fling(
      velocity: 2.0,
      spring: kDefaultFlingSpring,
    );
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _animation,
      builder: (context, child) => FadeTransition(
        opacity: _animation,
        child: child,
      ),
      child: const Text('Fading Content'),
    );
  }
}
```

## Tweens & Sequences

Interpolate values across custom types or chained sequence weight segments:

```dart
// Basic Tween
final colorAnim = ColorTween(
  begin: Colors.blue,
  end: Colors.red,
).animate(_controller);

// Multi-stage TweenSequence
final bounceAnim = TweenSequence<double>([
  TweenSequenceItem(
    tween: Tween<double>(begin: 0.0, end: 100.0),
    weight: 40.0,
  ),
  TweenSequenceItem(
    tween: Tween<double>(begin: 100.0, end: 80.0),
    weight: 20.0,
  ),
  TweenSequenceItem(
    tween: Tween<double>(begin: 80.0, end: 100.0),
    weight: 40.0,
  ),
]).animate(_controller);
```

## Implicitly Animated Widgets

DartNative includes Tier 1 native-backed implicitly animated widgets that animate changes automatically whenever properties update:

- **`AnimatedContainer`**: Smoothly interpolates `width`, `height`, `padding`, `margin`, `color`, and `decoration`.
- **`AnimatedSwitcher`**: Cross-fades between changing child widgets.
- **`AnimatedSize`**: Smoothly expands or collapses to match new child widget dimensions.
- **`AnimatedAlign` / `AnimatedPadding` / `AnimatedPositioned`**: Interpolates layout positioning and insets.
- **`AnimatedDefaultTextStyle`**: Smoothly interpolates font size, color, letter spacing, and line height.

```dart
AnimatedContainer(
  duration: const Duration(milliseconds: 250),
  curve: Curves.easeOut,
  width: _isExpanded ? 300.0 : 100.0,
  color: _isExpanded ? Colors.blue : Colors.grey,
  child: const Text('Tap to expand'),
)
```

## 3D Perspective Transforms (`Transform.perspective`)

Perform 3D rotations projected onto 2D planes natively:

```dart
Transform.perspective(
  rotateY: -0.5, // Angle in radians
  rotateX: 0.2,
  depth: 900.0, // Distance to viewer in logical px
  child: Image.asset('assets/cover.webp'),
)
```

## Gotchas

- **Ticker Lifecycle Muting:** Tickers are automatically muted when the app transitions to the background (`paused`/`inactive`), pausing elapsed stopwatch accumulation to preserve battery and thermal headroom.
- **`fling()` vs `animateTo()`:** Prefer `_controller.fling(velocity: ...)` for gesture-driven panel dismissals—it decelerates physically based on finger release speed without forcing fixed durations.
- **`AnimatedSize` Max Constraints:** `AnimatedSize` uses an internal maximum sentinel constraint (`_kAnimSizeMax`) during expansion and clears it upon animation completion to prevent accidental clipping.
