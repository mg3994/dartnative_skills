---
name: dartnative-grid-view
description: Display standard and Pinterest-style masonry scrollable grids in DartNative using GridView, GridView.count, GridView.builder, and MasonryGridView. Use when creating fixed-column grids, horizontal or vertical grids, or variable-height masonry layouts.
---

# DartNative GridView & Masonry Grid Guide

DartNative provides scrollable grid widgets including standard flex-backed `GridView` (`GridView.count`, `GridView.builder`) and Pinterest-style `MasonryGridView` (`MasonryGridView.count`, `MasonryGridView.builder`).

## Standard Grid (`GridView.count` & `GridView.builder`)

`GridView` arranges items evenly into fixed columns or rows using FlexLayout and Yoga:

```dart
import 'package:dartnative/dartnative.dart';

// Fixed Count Shorthand
Widget buildCategoryGrid(List<Category> categories) {
  return GridView.count(
    crossAxisCount: 3,
    mainAxisSpacing: 8.0,
    crossAxisSpacing: 8.0,
    childAspectRatio: 1.0,
    padding: const EdgeInsets.all(12.0),
    children: categories.map((cat) => CategoryTile(cat)).toList(),
  );
}

// On-demand Builder Variant
Widget buildDynamicGrid(List<Product> products) {
  return GridView.builder(
    gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
      crossAxisCount: 2,
      mainAxisSpacing: 10.0,
      crossAxisSpacing: 10.0,
      childAspectRatio: 0.75,
    ),
    itemCount: products.length,
    itemBuilder: (context, index) {
      return ProductCard(products[index]);
    },
  );
}
```

## Pinterest-Style Masonry Grid (`MasonryGridView`)

`MasonryGridView` creates a scrollable Pinterest-style grid that assigns items to the shortest column based on heights supplied via `itemHeightBuilder`:

```dart
Widget buildPinterestFeed(List<Photo> photos) {
  return MasonryGridView.builder(
    crossAxisCount: 2,
    mainAxisSpacing: 8.0,
    crossAxisSpacing: 8.0,
    itemCount: photos.length,
    itemHeightBuilder: (index) => photos[index].calculatedHeight,
    itemBuilder: (context, index) {
      return PhotoTile(photos[index]);
    },
  );
}
```

## Horizontal Scrolling Grids

Both `GridView` constructors accept `scrollDirection: Axis.horizontal`, where `crossAxisCount` specifies the number of rows and items scroll left-to-right:

```dart
GridView.count(
  scrollDirection: Axis.horizontal,
  crossAxisCount: 2, // 2 rows
  childAspectRatio: 1.2,
  children: [ ... ],
)
```

## FastGrid vs GridView

- **`GridView` / `MasonryGridView`**: Eagerly inflates child views into row/column flex containers. Ideal for small, bounded grids or embedded non-scrolling sections (`shrinkWrap: true`).
- **`FastGrid`**: Uses native cell recycling (`UICollectionView` on iOS / `RecyclerView` on Android). Essential for long or infinite image feeds to maintain O(visible) memory efficiency.

## Gotchas

- **Virtualization:** `GridView` builds all child widgets eagerly. For long lists or infinite feeds, prefer `FastGrid` to avoid large memory footprints.
- **`MasonryGridView` Height Builder:** Supply an accurate height calculation in `itemHeightBuilder(index)` so `MasonryGridView` can distribute items evenly across columns without waiting for native layout measurement passes.
- **Import Aliasing:** Always import UI types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
