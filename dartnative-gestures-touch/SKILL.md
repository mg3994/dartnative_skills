---
name: dartnative-gestures-touch
description: Detect touch gestures and raw pointer events in DartNative using GestureDetector, Listener, InkWell, and detail classes (TapDownDetails, ScaleUpdateDetails, DragUpdateDetails). Use when handling taps, double taps, long presses, pan/drag gestures, multi-touch pinch-to-zoom scaling, raw pointer streams, or touch highlights.
---

# DartNative Gestures & Touch Handling Guide

DartNative maps touch gestures directly to native platform gesture recognizers (`UIGestureRecognizer` on iOS and MotionEvent detectors on Android), eliminating custom Dart gesture disambiguation overhead while maintaining Flutter API compatibility.

## High-Level Gestures (`GestureDetector`)

`GestureDetector` detects taps, long presses, pan/drag movements, and multi-finger pinch-to-zoom scale gestures:

```dart
import 'package:dartnative/dartnative.dart';

class MyInteractiveCard extends StatefulWidget {
  const MyInteractiveCard({super.key});

  @override
  State<MyInteractiveCard> createState() => _MyInteractiveCardState();
}

class _MyInteractiveCardState extends State<MyInteractiveCard> {
  double _scale = 1.0;
  Offset _position = Offset.zero;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      behavior: HitTestBehavior.opaque,
      onTap: () => print('Card tapped'),
      onDoubleTap: () => print('Double tapped'),
      onLongPress: () => print('Long pressed'),

      // Drag/Pan Gestures
      onPanUpdate: (DragUpdateDetails details) {
        setState(() {
          _position += details.delta;
        });
      },

      // Multi-Touch Pinch-to-Zoom Scale
      onScaleStart: (ScaleStartDetails details) {
        print('Pinch started with ${details.pointerCount} fingers');
      },
      onScaleUpdate: (ScaleUpdateDetails details) {
        setState(() {
          // Apply cumulative scale factor relative to gesture start
          _scale = details.scale;
        });
      },
      onScaleEnd: (ScaleEndDetails details) {
        print('Pinch ended with velocity: ${details.velocity}');
      },
      child: Transform.scale(
        scale: _scale,
        child: Container(
          color: Colors.blue,
          padding: const EdgeInsets.all(24.0),
          child: const Text('Interactive Card'),
        ),
      ),
    );
  }
}
```

## Raw Pointer Events (`Listener`)

Unlike `GestureDetector` (which disambiguates gesture recognizers), `Listener` fires unconditionally on every raw touch event (`PointerDownEvent`, `PointerMoveEvent`, `PointerUpEvent`, `PointerCancelEvent`):

```dart
Listener(
  onPointerDown: (event) {
    print('Raw pointer down at local position: ${event.localPosition}');
  },
  onPointerMove: (event) {
    print('Raw pointer moved by delta: ${event.delta}');
  },
  onPointerUp: (event) => print('Raw pointer lifted'),
  child: const MyDrawingArea(),
)
```

## Touch Highlights & Feedback (`InkWell`)

`InkWell` provides touch feedback mapped to platform native highlights (`UIButton` alpha highlight on iOS, ripple feedback on Android):

```dart
InkWell(
  onTap: () => print('InkWell tapped'),
  borderRadius: BorderRadius.circular(8.0),
  splashColor: Colors.blue.withOpacity(0.2),
  highlightColor: Colors.grey.withOpacity(0.1),
  child: const Padding(
    padding: EdgeInsets.all(12.0),
    child: Text('Tap Me'),
  ),
)
```

## Details & Hit Testing Types

- **`HitTestBehavior`**: `opaque` (intercepts events over entire bounds), `deferToChild` (only hit tests child widgets), `translucent` (receives touch AND forwards event to underlying views).
- **`DragUpdateDetails`**: Exposes `delta` (`Offset`), `localPosition`, `pointerCount` (distinguishes 1-finger from 2-finger drags), and `primaryDelta` (for horizontal/vertical drag dispatchers).
- **`ScaleUpdateDetails`**: Exposes cumulative `scale` factor (1.0 = baseline, 2.0 = double size), `focalPoint` centroid, and `pointerCount`.

## Gotchas

- **Position Coordinates:** On `TapDownDetails` and `TapUpDetails`, use `localPosition` for coordinates inside the target view bounds (`globalPosition` is not populated by native recognizers and remains `Offset.zero`).
- **Cumulative Scale Factor:** In `onScaleUpdate`, `details.scale` represents the cumulative scale multiplier relative to when the gesture began. Apply `details.scale` directly to base transforms rather than accumulating per-frame deltas.
- **Pinch-to-Zoom Finger Count:** Pinch scaling requires $\ge 2$ fingers. Lifting a finger drops `pointerCount` to 1 and fires `onScaleEnd`.
