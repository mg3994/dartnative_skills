---
name: dartnative-plugin-development
description: Build custom native platform plugins and host native OS views in DartNative using NativeElement, ViewType, UIKitReconciler, FlexProps, ViewProps, and PluginMutation. Use when writing plugins, hosting native UI views, sending FFI mutations, or setting up native view layout properties.
---

# DartNative Plugin Development Guide

DartNative plugins allow hosting native OS views (such as platform-native video players, maps, or specialized native UI controls) directly within the DartNative view tree using `NativeElement` and FFI mutation channels.

## Imports for Plugin Authors

Plugin implementations should import `package:dartnative/plugin.dart` (which exports reconciler elements, mutations, and flags) alongside `package:dartnative/dartnative.dart`:

```dart
import 'dart:typed_data';
import 'package:dartnative/dartnative.dart';
import 'package:dartnative/plugin.dart';
```

## Creating a Native Hosted View Plugin

To host a native OS view, extend `NativeElement`, claim an integer `viewType` from its string key via `ViewType.claim(key)`, override `buildProps()`, and dispatch `PluginMutation` payloads using `emitMutation()`.

```dart
// 1. Claim unique integer viewType from string key
final int kNativeMapViewType = ViewType.claim('com.example.native_map_view');

class NativeMapView extends Widget {
  final String apiKey;
  final bool showTraffic;

  const NativeMapView({
    super.key,
    required this.apiKey,
    this.showTraffic = false,
  });

  @override
  Element createElement() => NativeMapElement(this);
}

class NativeMapElement extends NativeElement {
  NativeMapElement(NativeMapView super.widget);

  @override
  NativeMapView get widget => super.widget as NativeMapView;

  @override
  int get viewType => kNativeMapViewType;

  @override
  ViewProps buildProps() {
    return const ViewProps(
      clipsToBounds: true,
      userInteractionEnabled: true,
    );
  }

  @override
  void mount(Element? parent, UIKitReconciler rec) {
    super.mount(parent, rec);
    _configureNativeMap();
  }

  @override
  void update(Widget newWidget) {
    super.update(newWidget);
    _configureNativeMap();
  }

  void _configureNativeMap() {
    if (viewId == null) return;

    // Encodes payload into Uint8List bytes for high-speed FFI dispatch
    final Uint8List payloadBytes = Uint8List.fromList(widget.apiKey.codeUnits);

    // Send PluginMutation via Element.emitMutation()
    emitMutation(PluginMutation(
      viewId!,
      1, // eventTag identifier
      payloadBytes,
    ));
  }
}
```

## Plugin Architecture & Reconciler Surface

- **`NativeElement`**: The element node connecting DartNative's element tree to a platform view instance (`viewId`).
- **`ViewType.claim(key)`**: Maps string platform view identifiers to framework integer claim IDs.
- **`UIKitReconciler` / `DartNativeReconciler`**: Reconciles layout changes and sends view mutations to the underlying native engine via high-speed direct FFI calls.
- **`FlexProps` / `ViewProps`**: Instructs the native FlexLayout / Auto Layout engine on sizing, flex grow/shrink, margins, and alignment (`SetAlignSelf`, `SetFlexAspectRatio`, `SetFlexPositionType`, `SetFlexPositionInsets`).
- **`PluginMutation`**: Low-level binary mutation signal containing `(viewId, eventTag, Uint8List data)` dispatched to update platform view parameters without tearing down the native view.

## Debugging Plugin Diagnostics

Enable framework-level native diagnostic logs using `dnVerboseLog` or `dnTraceLog`:

```dart
import 'package:dartnative/platform.dart';

void main() {
  // Enables framework diagnostic log lines
  DartNativeLogger.run(verbose: true);

  // Or toggle runtime flags directly
  // dnVerboseLog = true;
  // dnTraceLog = true; // per-frame trace

  runApp(const MyApp());
}
```

## Gotchas for Plugin Creators

- **ViewType Types:** `NativeElement.viewType` returns an `int` (not a `String`). Use `ViewType.claim('my.plugin.view')` to map string view keys to integer claim IDs.
- **`buildProps` Override:** Override `buildProps()` (not `createProps()`) on `NativeElement` subclasses.
- **Direct Imports:** Plugins MUST import `package:dartnative/plugin.dart` instead of deep internal `src/reconciler/` paths.
- **Zero Lock Injection:** Do not import `package:flutter` in plugin packages; sitting directly on `dart:ui`/FFI keeps consumer project lock files free of Flutter SDK lock dependencies.
