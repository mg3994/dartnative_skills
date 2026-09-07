---
name: dartnative-visual-effects
description: Apply backdrop blur and image filtering effects in DartNative using BackdropFilter, ImageFiltered, and ImageFilter.blur. Use when creating frosted glass overlays, blurred background panels, soft text/icon effects, or handling platform-specific blur fallback behavior across iOS and Android APIs.
---

# DartNative Visual Effects Guide

DartNative supports platform-native visual effects (such as backdrop blur and child image filtering) using platform-native APIs (`UIVisualEffectView` and `CIFilter` on iOS; `RenderEffect` on Android API 31+).

## Backdrop Blur (`BackdropFilter`)

Applies a visual blur effect to the content **behind** the widget and renders its `child` sharp on top. Ideal for frosted glass translucent panels overlaying busy background layouts.

```dart
import 'package:dartnative/dartnative.dart';

Stack(
  children: [
    // Background content
    const MyBusyBackground(),

    // Translucent frosted glass overlay
    Positioned.fill(
      child: BackdropFilter(
        filter: ImageFilter.blur(sigmaX: 20.0, sigmaY: 20.0),
        child: Container(
          color: const Color(0x66FFFFFF),
          child: const Center(
            child: Text('Frosted Glass Header'),
          ),
        ),
      ),
    ),
  ],
)
```

### Platform Behavior Matrix (`BackdropFilter`)
- **iOS:** Uses `UIVisualEffectView` placed behind the host bounds. Captures and blurs live content behind the view—true native backdrop blur.
- **Android (API 31+):** Uses `view.setRenderEffect(RenderEffect.createBlurEffect(...))`. Note that Android's `RenderEffect` blurs the view's own rasterized output rather than behind-view content; place a translucent `Container` over the blurred region in a `Stack` to achieve the frosted overlay visual effect.
- **Android (API < 31):** `RenderEffect` is unavailable; gracefully degrades to a transparent passthrough. Provide a fallback tint/dim via `Container` decoration.

## Child Image Filtering (`ImageFiltered`)

Blurs or filters the widget's **own** child rendering while leaving content below it sharp. Use for frosted text, blurred thumbnails, or soft icon effects.

```dart
Stack(
  children: [
    Image.asset('photo.jpg'),
    Positioned.fill(
      child: Center(
        child: ImageFiltered(
          imageFilter: ImageFilter.blur(sigmaX: 10.0, sigmaY: 10.0),
          child: const Text(
            'Soft Blurred Text',
            style: TextStyle(fontSize: 48.0, color: Colors.white),
          ),
        ),
      ),
    ),
  ],
)
```

### Platform Behavior Matrix (`ImageFiltered`)
- **Android (API 31+):** Uses zero-snapshot, GPU-accelerated `RenderEffect.createBlurEffect()` on the view, blurring its own child output.
- **Android (API < 31):** No-op passthrough.
- **iOS:** Captures the child view into a `UIImage` snapshot, applies `CIFilter("CIGaussianBlur")`, and displays the blurred snapshot on a `UIImageView`. The snapshot is cached and invalidated only when child properties change to maintain performance.

## Gotchas

- **Supported Filters:** Currently, only `ImageFilter.blur(sigmaX: ..., sigmaY: ...)` is supported. Non-blur filters resolve to zero sigma `(0, 0)` no-ops via `backdropFilterSigma`.
- **Android API Limits:** Always test blur effects on Android API < 31 to ensure adequate visual fallback contrast using semi-transparent background colors in `Container`.
