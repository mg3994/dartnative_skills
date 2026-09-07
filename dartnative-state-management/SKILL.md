---
name: dartnative-state-management
description: Manage state in DartNative apps using reactive primitives (Signal, Computed, effect), context subscriptions (.watch(context)), and dependency injection (Provided). Use when managing local or global reactive state, watching signals in build methods, running side effects, or migrating ChangeNotifier/ValueNotifier.
---

# DartNative Reactive State Management Guide

DartNative provides fine-grained, signal-based reactive state primitives designed to minimize rebuilds and work seamlessly with platform-native reconcilers.

## Core Primitives

### 1. `Signal<T>` — Writable Reactive Value
Create a writable state atom with `signal<T>(initialValue)`.

```dart
import 'package:dartnative/dartnative.dart';

// Top-level or property inside a controller
final counter = signal<int>(0);

// Write value from anywhere (callbacks, timers, FFI events)
counter.value++;
counter.value = 10;

// Functional update
counter.update((current) => current * 2);
```

### 2. `Computed<T>` — Read-only Derived Value
Create derived state with `computed<T>(fn)`. Recomputes lazily and cached until source signals change.

```dart
final items = signal<List<Item>>([]);
final searchQuery = signal<String>('');

// Derived reactive value
final filteredItems = computed(() {
  final query = searchQuery.value.toLowerCase();
  return items.value.where((item) => item.title.toLowerCase().contains(query)).toList();
});
```

### 3. `effect()` — Synchronous Side Effects
Run side effects when dependent signals change. Returns a `VoidCallback` to stop listening.

```dart
final stopLogging = effect(() {
  print('Current query: ${searchQuery.value}');
});

// Cancel effect subscription when done
stopLogging();
```

*Note:* `effect()` runs synchronously on the Dart isolate and does not interact with `BuildContext` or schedule widget rebuilds. Use it for persistence, logging, sync, or external native bridge updates.

## UI Subscriptions (`.watch(context)`)

Subscribe elements to `Signal` or `Computed` inside a widget's `build` method. Subscriptions are automatically refreshed on rebuild and cleared on unmount.

```dart
class CounterWidget extends StatelessWidget {
  const CounterWidget({super.key});

  @override
  Widget build(BuildContext context) {
    // Subscribes context to changes in counter
    final count = counter.watch(context);

    return Text('Count: $count');
  }
}
```

## Migrating `ChangeNotifier` and `Listenable`

Bridge existing `ChangeNotifier`, `ValueNotifier`, or `AnimationController` instances without modifying their class definitions using the `.watch(context)` extension:

```dart
class SettingsNotifier extends ChangeNotifier {
  String locale = 'en';
  void setLocale(String val) {
    locale = val;
    notifyListeners();
  }
}

final settingsNotifier = SettingsNotifier();

// In build method:
Widget build(BuildContext context) {
  // Uses cascade operator to subscribe context and read property
  final settings = settingsNotifier..watch(context);
  return Text('Locale: ${settings.locale}');
}
```

## Dependency Injection (`Provided<T>`)

Pass state or controllers down a subtree without prop drilling using `Provided<T>` (a light `InheritedWidget` wrapper).

```dart
// 1. Wrap a subtree:
Provided<TaskStore>(
  value: myTaskStore,
  child: const TaskListScreen(),
)

// 2. Read from any descendant:
class TaskListScreen extends StatelessWidget {
  const TaskListScreen({super.key});

  @override
  Widget build(BuildContext context) {
    final store = Provided.of<TaskStore>(context);
    // For reactive signals provided down the tree:
    final tasks = Provided.of<Signal<List<Task>>>(context).watch(context);

    return ListView.builder(...);
  }
}
```

## Best Practices & Gotchas

- **Do Not Call `.watch(context)` Outside `build()`:** Calling `.watch(context)` in lifecycle callbacks, event handlers, or async functions will fail or subscribe the wrong context element. Use `.value` or `effect()` instead.
- **Coalesced Rebuilds:** Reading multiple signals via `.watch(context)` within a single `build()` call automatically coalesces updates into a single native element rebuild pass.
- **Memory Safety:** Signal subscriptions on elements are auto-disposed when the element is unmounted—no manual `removeListener` calls required in widget code.
