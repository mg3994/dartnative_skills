---
name: dartnative-fast-grid
description: Create virtualized cell-recycling grids in DartNative using FastGrid and FastGridController. Use when rendering large scrollable image or card grids backed by UICollectionView/RecyclerView, controlling index-based scrolling (jumpToItem/scrollToItem), bounding off-screen cell memory with keepAliveCount, or tracking grid scroll position with FastScrollCallback.
---

# DartNative Virtualized FastGrid Guide

`FastGrid` is DartNative's virtualized, cell-recycling grid component backed directly by native `UICollectionView` (on iOS) and `RecyclerView` with a grid layout manager (on Android).

Unlike standard layout grids that inflate all cells into view trees, `FastGrid` delegates cell layout and recycling to native platform controls, maintaining **O(visible)** memory performance regardless of total item count.

## Basic Usage

```dart
import 'package:dartnative/dartnative.dart';

class MyImageGrid extends StatelessWidget {
  final List<String> imageUrls;

  const MyImageGrid({super.key, required this.imageUrls});

  @override
  Widget build(BuildContext context) {
    return FastGrid(
      itemCount: imageUrls.length,
      crossAxisCount: 3,
      childAspectRatio: 1.0,
      mainAxisSpacing: 4.0,
      crossAxisSpacing: 4.0,
      padding: const EdgeInsets.all(8.0),
      itemBuilder: (context, index) {
        return Image.network(
          imageUrls[index],
          fit: BoxFit.cover,
        );
      },
    );
  }
}
```

## Programmatic Index-Based Scrolling (`FastGridController`)

`FastGrid` uses index-based scrolling rather than pixel offsets—matching native platform conventions (`UICollectionView.scrollToItem` / `RecyclerView.scrollToPosition`):

```dart
final _gridController = FastGridController();

Widget buildGridWithController(BuildContext context) {
  return Column(
    children: [
      Row(
        children: [
          Button(
            title: 'Jump to #50',
            onPressed: () => _gridController.jumpToItem(50, alignment: 0.5),
          ),
          Button(
            title: 'Scroll to Top',
            onPressed: () => _gridController.scrollToItem(0),
          ),
        ],
      ),
      Expanded(
        child: FastGrid(
          itemCount: 1000,
          crossAxisCount: 4,
          gridController: _gridController,
          itemBuilder: (context, index) => Center(child: Text('Item $index')),
        ),
      ),
    ],
  );
}
```

## Bounding Memory with `keepAliveCount`

Use `keepAliveCount` to bound cell memory overhead on long grids. Off-window cells beyond `keepAliveCount` release their built content (bitmaps, network image handles, native view elements) while preserving their fixed slot geometry:

```dart
FastGrid(
  itemCount: 10000,
  crossAxisCount: 3,
  keepAliveCount: 10, // Keeps ~visible + 10 cells built on each viewport side
  itemBuilder: (context, index) {
    return Card(child: Text('Card $index'));
  },
)
```

## Scroll Callbacks (`onScroll`)

Listen to scroll progress for infinite scrolling or FAB/header visibility toggles:

```dart
FastGrid(
  itemCount: items.length,
  crossAxisCount: 2,
  onScroll: (offset, maxScrollExtent, velocity, userInteracted) {
    if (offset >= maxScrollExtent - 200) {
      // Near bottom — trigger load more
      loadMoreItems();
    }
  },
  itemBuilder: (context, i) => ProductTile(item: items[i]),
)
```

## Gotchas

- **Fixed Cell Geometry:** Cell dimensions in `FastGrid` are computed directly from `crossAxisCount` + `childAspectRatio` (or `mainAxisExtent`) and never depend on item content. This guarantees zero layout jumping or scroll stutters while network image thumbnails load.
- **Index-Based Controller:** Prefer `FastGridController` (`jumpToItem` / `scrollToItem`) over pixel-based `ScrollController` for reliable platform-native scrolling behavior.
- **Off-Window State Reset:** Cells that scroll past `keepAliveCount` have their item-level element states dismounted (similar to Flutter `cacheExtent` behavior). Always store persistent item selection/form state in your top-level signal or data model.
