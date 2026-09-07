---
name: dartnative-liquid-glass
description: Configure iOS 26 Liquid Glass material containers and fluid merging groups in DartNative using GlassEffectContainer, GlassEffectGroup, and GlassStyle. Use when creating frosted or clear Liquid Glass containers, applying custom glass tints/shadows/brightness, enabling interactive touch-reactive glass (isInteractive), or fluidly merging adjacent glass elements.
---

# DartNative Liquid Glass (iOS 26) Guide

DartNative supports iOS 26's **Liquid Glass** design language via `GlassEffectContainer` and `GlassEffectGroup`. Liquid Glass uses native `UIGlassEffect` and `UIGlassContainerEffect` via `UIVisualEffectView`.

## Glass Effect Container (`GlassEffectContainer`)

Wraps any child in an iOS 26 Liquid Glass material rendered behind the child:

```dart
import 'package:dartnative/dartnative.dart';

Widget buildGlassCard(BuildContext context) {
  return GlassEffectContainer(
    borderRadius: BorderRadius.circular(20.0),
    tint: const Color(0x33FFFFFF), // Optional tint
    style: GlassStyle.regular,     // regular or clear
    interactive: true,             // Enables UIGlassEffect.isInteractive touch physics
    shadow: const BoxShadow(
      color: Colors.black26,
      blurRadius: 10.0,
      offset: Offset(0, 4),
    ),
    child: Padding(
      padding: const EdgeInsets.all(16.0),
      child: Row(
        children: const [
          Icon(CupertinoIcons.star_fill, color: Colors.amber),
          SizedBox(width: 8.0),
          Text('Liquid Glass Header'),
        ],
      ),
    ),
  );
}
```

## Fluid Glass Merging Groups (`GlassEffectGroup`)

Groups multiple descendant `GlassEffectContainer`s or glass buttons so they fluidly blend into a single merging blob when within `spacing` points:

```dart
GlassEffectGroup(
  spacing: 40.0, // Merge distance in points
  child: Row(
    mainAxisAlignment: MainAxisAlignment.spaceEvenly,
    children: [
      GlassEffectContainer(
        borderRadius: BorderRadius.circular(24.0),
        child: IconButton(
          icon: const Icon(CupertinoIcons.play_fill),
          onPressed: () {},
        ),
      ),
      GlassEffectContainer(
        borderRadius: BorderRadius.circular(24.0),
        child: IconButton(
          icon: const Icon(CupertinoIcons.pause_fill),
          onPressed: () {},
        ),
      ),
    ],
  ),
)
```

## Glass Styles & Brightness Pinning

- **`GlassStyle.regular`**: Standard frosted Liquid Glass material.
- **`GlassStyle.clear`**: More transparent Liquid Glass variant matching `UIButton.Configuration.clearGlass()`.
- **`brightness`**: Pins the glass capsule tone (`Brightness.dark` / `Brightness.light`) independent of system theme. Crucial for dark-by-design screens in light OS themes so glass capsules don't wash out.

```dart
GlassEffectContainer(
  style: GlassStyle.clear,
  brightness: Brightness.dark, // Keeps capsule dark on dark screens regardless of system theme
  child: const Text('Dark Mode Glass', style: TextStyle(color: Colors.white)),
)
```

## Platform Availability Matrix

- **iOS 26+**: Renders native `UIGlassEffect` and `UIGlassContainerEffect`.
- **iOS < 26 & Android**: Plain pass-through container—children render normally with no effect. Liquid Glass is an iOS 26-only design material, so there is no fake blur fallback.

## Gotchas

- **Interactive Glass Clipping:** Setting `interactive: true` causes Liquid Glass to press and scale past the container's bounds on touch. Do not wrap interactive glass inside a clipping box (such as `ClipRRect`) or its press animation will get clipped.
- **Shadow Placement:** Attach shadows directly to `GlassEffectContainer.shadow` rather than wrapping in a rounded container box to preserve interactive scale room.
- **Import Aliasing:** Always import types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
