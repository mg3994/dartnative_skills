---
name: dartnative-fast-list
description: Create high-performance cell-recycling lists in DartNative using FastList and FastListController. Use when building long scrollable lists backed by UITableView/RecyclerView, controlling index-based scrolling (jumpToItem/scrollToItem/scrollToBottom), bounding cell memory with keepAliveCount, or tracking visible item ranges with onVisibleRange.
---

# DartNative Virtualized FastList Guide

`FastList` is DartNative's high-performance list widget designed for maximum native scrolling and cell-recycling performance.

Unlike `ListView` (which is a Flutter-compatibility shim), `FastList` maps directly to native list primitives (`UITableView` on iOS and `RecyclerView` on Android) with true O(1) cell dequeue/recycling and direct index-based scrolling.

## FastList vs ListView Comparison

| Feature | `ListView.builder` | `FastList` |
|---|---|---|
| **Platform Backing** | `UIScrollView` + Auto Layout | `UITableView` (iOS) / `RecyclerView` (Android) |
| **Cell Recycling** | None (all view nodes present) | True native `dequeue/recycle` |
| **Index Jumping** | Computed offset heuristic | Direct `NSIndexPath` / `scrollToPosition` |
| **Memory Scaling** | O(N) memory grow | O(visible) with `keepAliveCount` |

## Basic Usage

```dart
import 'package:dartnative/dartnative.dart';

final _listController = FastListController();

Widget buildFastList(List<Item> items) {
  return FastList(
    itemCount: items.length,
    controller: _listController,
    padding: const EdgeInsets.symmetric(vertical: 8.0, horizontal: 16.0),
    showScrollBar: true,
    itemBuilder: (context, index) {
      final item = items[index];
      return ListTile(
        title: Text(item.title),
        subtitle: Text(item.description),
      );
    },
  );
}
```

## Programmatic Index Scrolling (`FastListController`)

`FastListController` provides direct index-based scrolling without calculating pixel offsets:

```dart
// Instantly jump to item #100 at viewport center (no animation)
_listController.jumpToItem(100, alignment: 0.5);

// Smoothly animate scroll to item #100
_listController.scrollToItem(100, alignment: 0.0);

// Animate with custom duration and curve (drives contentOffset via UIView.animate)
_listController.animateToItem(
  100,
  duration: const Duration(milliseconds: 500),
  curve: Curves.easeInOut,
);

// Scroll to bottom (ideal for chat message feeds)
_listController.scrollToBottom(animated: true);
```

## Memory Management (`keepAliveCount` & `stableItems`)

- **`keepAliveCount`**: Keeps only ~visible + `keepAliveCount` items built on each viewport side. Off-window cells dispose their built content (bitmaps, native view nodes) while keeping the row slot pinned at its measured height to prevent scroll jumps.
- **`stableItems`**: Instructs `FastList` that existing rows never change on `setState()` (append-only pagination). Reduces re-render diffing from O(N) to O(changed).

```dart
FastList(
  itemCount: 10000,
  keepAliveCount: 20, // Keeps visible + 20 rows built above and below
  stableItems: true,  // Append-only feed optimization
  itemBuilder: (context, index) => MessageRow(messages[index]),
)
```

## Scroll & Visible Range Callbacks

- **`onScroll`**: Replaces Flutter's `ScrollController` listener with `(offset, maxExtent, viewport, dragging)`.
- **`onVisibleRange`**: Fires when the visible item range changes (`firstVisible`..`lastVisible`).

```dart
FastList(
  itemCount: items.length,
  onScroll: (offset, maxExtent, viewport, dragging) {
    if (offset >= maxExtent - 300) {
      // Trigger load-more pagination
      fetchNextPage();
    }
  },
  onVisibleRange: (firstVisible, lastVisible) {
    print('Visible items on screen: $firstVisible to $lastVisible');
  },
  itemBuilder: (context, i) => ItemTile(items[i]),
)
```

## Gotchas

- **Index-Based Scrolling:** Use `FastListController` rather than pixel-based offsets for precise programmatic scrolling.
- **Reverse List Semantics:** In `reverse: true` lists (e.g. chat screens), `onVisibleRange` automatically converts index bounds from native order to Dart index order.
- **Cell State Disposal with `keepAliveCount`:** Like Flutter's `cacheExtent`, cells disposed past `keepAliveCount` lose local `State` (e.g., active text field inputs). Always hold persistent item selection or input state in your model or signal.
