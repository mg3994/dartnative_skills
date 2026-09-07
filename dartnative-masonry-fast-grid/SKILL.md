---
name: dartnative-masonry-fast-grid
description: Create virtualized Pinterest-style staggered masonry grids in DartNative using MasonryFastGrid and FastGridController. Use when rendering large staggered variable-height image or card feeds backed by UICollectionView/RecyclerView, controlling index-based scrolling, bounding off-screen cell memory with keepAliveCount, or tracking scroll callbacks with FastScrollCallback.
---

# DartNative Virtualized MasonryFastGrid Guide

`MasonryFastGrid` is DartNative's virtualized, cell-recycling staggered (Pinterest-style) grid widget backed by custom native layout managers (`UICollectionViewLayout` on iOS and `StaggeredGridLayoutManager` on Android).

Unlike `MasonryGridView` (which builds all items eagerly with Yoga layout), `MasonryFastGrid` computes cell frames up-front from `itemHeightBuilder` and only instantiates **visible cells**—maintaining **O(visible)** memory performance regardless of total item count.

## Basic Usage

```dart
import 'package:dartnative/dartnative.dart';

class MyPinterestFeed extends StatelessWidget {
  final List<FeedItem> items;

  const MyPinterestFeed({super.key, required this.items});

  @override
  Widget build(BuildContext context) {
    return MasonryFastGrid(
      itemCount: items.length,
      crossAxisCount: 2,
      mainAxisSpacing: 8.0,
      crossAxisSpacing: 8.0,
      padding: const EdgeInsets.all(8.0),
      itemHeightBuilder: (index) => items[index].calculatedHeight,
      itemBuilder: (context, index) {
        return FeedCardTile(items[index]);
      },
    );
  }
}
```

## Programmatic Index Scrolling (`FastGridController`)

`MasonryFastGrid` shares `FastGridController` with `FastGrid` for native index-based jump and smooth scroll operations:

```dart
final _gridController = FastGridController();

Widget buildMasonryWithController(BuildContext context) {
  return Column(
    children: [
      Row(
        children: [
          Button(
            title: 'Jump to #100',
            onPressed: () => _gridController.jumpToItem(100, alignment: 0.5),
          ),
          Button(
            title: 'Scroll Top',
            onPressed: () => _gridController.scrollToItem(0),
          ),
        ],
      ),
      Expanded(
        child: MasonryFastGrid(
          itemCount: 5000,
          crossAxisCount: 2,
          gridController: _gridController,
          itemHeightBuilder: (i) => (i % 3 + 1) * 120.0,
          itemBuilder: (context, i) => Card(child: Text('Card #$i')),
        ),
      ),
    ],
  );
}
```

## Bounding Cell Memory (`keepAliveCount`)

Use `keepAliveCount` to bound memory usage on long staggered feeds. Off-window cells beyond `keepAliveCount` release their built content (bitmaps, network image handles, native view elements) while preserving their `itemHeightBuilder`-driven layout slot:

```dart
MasonryFastGrid(
  itemCount: 10000,
  crossAxisCount: 2,
  keepAliveCount: 15, // Keeps ~visible + 15 cells built on each viewport side
  itemHeightBuilder: (i) => feedItems[i].height,
  itemBuilder: (context, i) => ImageTile(feedItems[i]),
)
```

## Scroll Progress Callbacks (`onScroll`)

Track scroll updates for infinite loading or FAB visibility toggles:

```dart
MasonryFastGrid(
  itemCount: feedItems.length,
  crossAxisCount: 2,
  itemHeightBuilder: (i) => feedItems[i].height,
  onScroll: (offset, maxExtent, viewport, dragging) {
    if (offset >= maxExtent - 300) {
      loadMoreFeedItems();
    }
  },
  itemBuilder: (context, i) => FeedTile(feedItems[i]),
)
```

## Gotchas

- **Up-Front Item Heights:** `itemHeightBuilder(index)` must return a positive height in logical pixels. The native layout engine uses these heights to compute staggered cell geometry before cells are instantiated—preventing layout jumps or scroll stutters.
- **`MasonryGridView` vs `MasonryFastGrid`:** Use `MasonryGridView` for small, non-scrolling, or `shrinkWrap: true` embedded sections; use `MasonryFastGrid` for long or infinite feeds to preserve native memory efficiency.
- **Import Aliasing:** Always import UI types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
