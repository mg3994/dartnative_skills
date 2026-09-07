---
name: dartnative-plugin-development
description: Build custom native platform plugins and host native OS views in DartNative using NativeElement, UIKitReconciler, FlexProps, ViewProps, and PluginMutation. Use when writing plugins, hosting native UI views, sending mutations over FFI, or setting up native view layout properties.
---

# DartNative Plugin Development Guide

DartNative plugins allow hosting native OS views (such as platform-native video players, maps, or specialized native UI controls) directly within the DartNative view tree using `NativeElement` and FFI mutation channels.

## Imports for Plugin Authors

Plugin implementations should import `package:dartnative/plugin.dart` (which exports reconciler elements, mutations, and flags) alongside `package:dartnative/dartnative.dart`:

```dart
import 'package:dartnative/dartnative.dart';
import 'package:dartnative/plugin.dart';
```

## Creating a Native Hosted View Plugin

To host a native OS view, extend `NativeElement` and declare its platform `ViewType` key and layout properties (`ViewProps` / `FlexProps`).

```dart
// 1. Declare ViewType key matching platform registration
const String kNativeMapViewType = 'com.example.native_map_view';

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
  String get viewType => kNativeMapViewType;

  @override
  ViewProps createProps() {
    return ViewProps(
      flexProps: FlexProps(
        flexGrow: 1.0,
      ),
    );
  }

  @override
  void mount(Element? parent, Object? newSlot) {
    super.mount(parent, newSlot);
    // Send initial configuration to native view
    _configureNativeMap();
  }

  @override
  void update(NativeMapView newWidget) {
    super.update(newWidget);
    _configureNativeMap();
  }

  void _configureNativeMap() {
    // Send mutation over native FFI channel
    sendPluginMutation(PluginMutation(
      viewId: viewId,
      action: 'setApiKey',
      payload: {'apiKey': widget.apiKey, 'traffic': widget.showTraffic},
    ));
  }
}
```

## Plugin Architecture & Reconciler Surface

- **`NativeElement`**: The element node connecting DartNative's element tree to a platform view instance (`ViewId`).
- **`UIKitReconciler` / `DartNativeReconciler`**: Reconciles layout changes and sends view mutations to the underlying native engine via high-speed direct FFI calls.
- **`FlexProps` / `ViewProps`**: Instructs the native FlexLayout / Auto Layout engine on sizing, flex grow/shrink, margins, and alignment (`SetAlignSelf`, `SetFlexAspectRatio`, `SetFlexPositionType`, `SetFlexPositionInsets`).
- **`PluginMutation` / `ViewMutation`**: Imperative mutation signals dispatched from Dart to update platform view parameters without tearing down or re-creating the native view.

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

- **Direct Imports:** Plugins MUST import `package:dartnative/plugin.dart` instead of deep internal `src/reconciler/` paths.
- **View Lifecycle:** Native view instances are assigned a unique `ViewId` upon mounting. Clean up FFI resources or native listeners in element unmount/dispose overrides.
- **Zero Lock Injection:** Do not import `package:flutter` in plugin packages; sitting directly on `dart:ui`/FFI keeps consumer project lock files free of Flutter SDK lock dependencies.
