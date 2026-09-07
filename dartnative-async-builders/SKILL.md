---
name: dartnative-async-builders
description: Use reactive and asynchronous builder widgets in DartNative including LayoutBuilder, ValueListenableBuilder, StreamBuilder, and FutureBuilder. Use when adapting responsive layouts to BoxConstraints, listening to ValueNotifier/ValueListenable, rebuilding on Stream events, or resolving Future computations.
---

# DartNative Async & Reactive Builders Guide

DartNative includes Flutter-compatible reactive and async builder widgets (`LayoutBuilder`, `ValueListenableBuilder`, `StreamBuilder`, `FutureBuilder`) that allow rebuilding subtrees dynamically based on constraints or asynchronous data sources.

## Responsive Layouts (`LayoutBuilder`)

Adapts layout widgets responsively according to incoming `BoxConstraints` from the parent view:

```dart
import 'package:dartnative/dartnative.dart';

Widget buildResponsiveLayout(BuildContext context) {
  return LayoutBuilder(
    builder: (context, constraints) {
      if (constraints.maxWidth > 600) {
        return const WideDesktopLayout();
      } else {
        return const CompactMobileLayout();
      }
    },
  );
}
```

## Value Notifiers (`ValueListenableBuilder`)

Listens to `ValueListenable` instances (such as `ValueNotifier<T>`) and rebuilds the builder subtree when the value updates:

```dart
final counterNotifier = ValueNotifier<int>(0);

Widget buildCounter(BuildContext context) {
  return ValueListenableBuilder<int>(
    valueListenable: counterNotifier,
    builder: (context, value, child) {
      return Text('Counter value: $value');
    },
  );
}
```

## Stream Subscriptions (`StreamBuilder`)

Subscribes to Dart `Stream<T>` events and updates UI states via `AsyncSnapshot<T>`:

```dart
Widget buildLiveMessages(Stream<String> messageStream) {
  return StreamBuilder<String>(
    stream: messageStream,
    initialData: 'Connecting...',
    builder: (context, snapshot) {
      if (snapshot.connectionState == ConnectionState.waiting) {
        return const ProgressIndicator();
      }
      if (snapshot.hasError) {
        return Text('Error: ${snapshot.error}');
      }
      return Text(snapshot.data ?? 'No messages');
    },
  );
}
```

## Async Futures (`FutureBuilder`)

Rebuilds UI based on `Future<T>` completion states:

```dart
Widget buildUserProfile(Future<User> fetchUserFuture) {
  return FutureBuilder<User>(
    future: fetchUserFuture,
    builder: (context, snapshot) {
      switch (snapshot.connectionState) {
        case ConnectionState.waiting:
        case ConnectionState.active:
          return const ProgressIndicator();
        case ConnectionState.done:
          if (snapshot.hasError) {
            return Text('Failed to load user: ${snapshot.error}');
          }
          final user = snapshot.data!;
          return Text('User: ${user.name}');
        case ConnectionState.none:
          return const Text('No active fetch task.');
      }
    },
  );
}
```

## Gotchas

- **Flutter Porting Parity:** `LayoutBuilder`, `ValueListenableBuilder`, `StreamBuilder`, and `FutureBuilder` are fully Flutter-compatible API equivalents—no code changes are required when porting Flutter builders to DartNative.
- **Future Memoization:** Pass a memoized `Future` instance (e.g. created in `initState()` or a signal) to `FutureBuilder.future` rather than instantiating the Future directly inside `build()`, preventing redundant network or async re-executions on widget rebuilds.
