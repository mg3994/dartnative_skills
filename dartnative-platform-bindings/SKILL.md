---
name: dartnative-platform-bindings
description: Develop, debug, and optimize low-level platform bindings and FFI reconciler bridges between Dart and native Android/iOS engines in DartNative. Use when inspecting IOSNativeBindings, understanding DNInitProvider zero-config startup, handling native text/paragraph measurement, image precaching, gesture callbacks, or low-level view reconciliation.
---

# DartNative Platform Bindings & FFI Bridge Guide

DartNative operates directly on top of `dart:ui` and low-level FFI function pointer channels to communicate directly with platform UI engines (UIKit on iOS and Android Views/Kotlin runtime) without depending on the Flutter framework runtime.

## Zero-Config Android Startup (`DNInitProvider`)

On Android, `dartnative_android` initializes lifecycle and back-button hooks automatically using ContentProvider startup initialization:

```xml
<provider
    android:name="com.dartnative.runtime.DNInitProvider"
    android:authorities="${applicationId}.dn-init"
    android:exported="false" />
```

- **`DNInitProvider`**: Installs `DNActivityHooks` on app boot up.
- **`DNActivityHooks`**: Automatically forwards system back-button presses to Dart's `Navigator.pop` stack and manages warm activity recreate / re-attachment.

## Low-Level Reconciler & FFI Symbol Dispatch

`IOSNativeBindings` and `AndroidNativeBindings` implement `NativeBinding` interface and load direct FFI function pointers (`loadSymbols()`) for atomic UI operations.

```dart
import 'package:dartnative/dartnative.dart';

// Accessing system metrics and low-level platform properties directly
final isiOS26Design = registeredBindings?.getIsIOS26() ?? false;
final pixelRatio = registeredBindings?.getDevicePixelRatio() ?? 1.0;
```

### Reconciler Callback Registration Flow
Events (gestures, scroll position, text edits, segment controls) register callback function pointers mapped to unique `callbackId` integers:

```dart
final reconciler = DartNativeReconciler(IOSNativeBindings.instance);

// Register low-level touch callback
final tapCallbackId = reconciler.registerTapUpCallback((x, y) {
  print('Native tap at x: $x, y: $y');
});

// Release FFI callback registration when view unmounts
reconciler.releaseCallback(tapCallbackId);
```

## Native Text & Paragraph Measurement

Unlike Flutter's engine-rendered text, DartNative measures native system fonts (`UILabel` / `TextView` / `StaticLayout`) over FFI before view inflation:

- **`measureText` / `measureParagraph`**: Measures text spans against constraints, returning width, height, line metrics, and character offset positions.
- **Font Spans (`DNTypefaceSpan`, `FontVariationSettingsSpan`)**: Encodes OpenType features, variable font variations, and exact line-height distributions directly into native attributed strings (`NSAttributedString` / `SpannableStringBuilder`).

## Image Loading & Pre-caching (`DNCoilHelper` / `ImageCache`)

Native image views are powered by Coil on Android and SDWebImage/CoreAnimation on iOS.

- **Pre-caching:** Pre-loads network, file, and asset images directly into native memory/disk caches before screen navigation.
- **Aspect Ratio Queries:** `getImageAspectRatio(viewId)` queries the decoded native bitmap dimensions to layout placeholders cleanly without layout thrashing.

## Low-Level View Mutations

View updates are batched into a list of `ViewMutation` instances and flushed atomically during frame commits:

```dart
// Enqueue mutation to hide or update native view properties
reconciler.enqueueMutation(SetViewHidden(viewId, true));
reconciler.scheduleMutationFlush();
```

## Gotchas

- **Zero Lock Injection:** Never import `package:flutter` inside platform binding extensions—keep all FFI bindings directly on `dart:ui` or `package:ffi` so consumer lockfiles remain Flutter SDK-free.
- **Callback Disarming:** Always disarm and release callback IDs (`releaseCallback(id)`) in element unmount logic to prevent native memory leaks or stale function invocations.
