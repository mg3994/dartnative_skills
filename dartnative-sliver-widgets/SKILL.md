---
name: dartnative-sliver-widgets
description: Guide for sliver scroll layouts in DartNative including CustomScrollView, SliverList, SliverGrid, SliverAppBar, and FlexibleSpaceBar.
---

# DartNative Sliver Widgets

DartNative provides sliver scroll components (`CustomScrollView`, `SliverList`, `SliverGrid`, `SliverAppBar`, `SliverToBoxAdapter`) to construct custom collapsing and multi-layout scroll views.

---

## 1. CustomScrollView

Main scroll container for sliver components.

```dart
CustomScrollView({
  List<Widget> slivers = const [],
  ScrollController? controller,
  ListController? listController,
  ScrollPhysics? physics,
  Axis scrollDirection = Axis.vertical,
  EdgeInsetsGeometry? padding,
})
```

---

## 2. SliverList & SliverGrid

Linear and 2D grid sliver arrays.

```dart
// SliverList with explicit children
SliverList.list(
  children: [Widget1(), Widget2()],
)

// SliverList with builder
SliverList.builder(
  itemCount: 100,
  itemBuilder: (context, index) => Text('Item $index'),
)

// SliverGrid with fixed cross-axis count
SliverGrid.count(
  crossAxisCount: 2,
  mainAxisSpacing: 8,
  crossAxisSpacing: 8,
  children: [Tile1(), Tile2()],
)
```

---

## 3. SliverAppBar & FlexibleSpaceBar

Collapsing app bar integrated with scroll view.

```dart
SliverAppBar(
  pinned: true,
  expandedHeight: 200,
  flexibleSpace: FlexibleSpaceBar(
    title: Text('Collapsing Title'),
    background: Image.network('https://example.com/banner.jpg'),
  ),
)
```

---

## 4. Helper Slivers

- **`SliverToBoxAdapter`**: Wraps a single non-sliver widget inside a `CustomScrollView`.
- **`SliverPadding`**: Insets a sliver with padding.
- **`SliverFillRemaining`**: Expands child to fill remaining viewport space.

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildSliverPage() {
  return CustomScrollView(
    slivers: [
      const SliverAppBar(
        pinned: true,
        title: Text('Sliver Layout'),
      ),
      SliverToBoxAdapter(
        child: Container(
          height: 80,
          color: Colors.blue.shade100,
          child: const Center(child: Text('Header Section')),
        ),
      ),
      SliverGrid.count(
        crossAxisCount: 2,
        children: List.generate(4, (i) => Card(child: Text('Card $i'))),
      ),
      SliverList.builder(
        itemCount: 20,
        itemBuilder: (context, index) => ListTile(title: Text('Row $index')),
      ),
    ],
  );
}
```

---

## Platform Compatibility Matrix

| Widget | iOS Implementation | Android Implementation |
|---|---|---|
| `CustomScrollView` | `UIScrollView` | Native ScrollView |
| `SliverAppBar` | `UINavigationBar` + Collapsing Header | Material 3 Collapsing Top Bar |
