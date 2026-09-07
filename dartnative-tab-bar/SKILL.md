---
name: dartnative-tab-bar
description: Configure native bottom navigation bars in DartNative using BottomNavigationBar, BottomNavigationBarItem, and TabBarScrollBehavior. Use when creating tabbed navigation, handling tab selections, configuring search destinations with UISearchTab, customizing tab colors/styles, or setting scroll minimize behavior on iOS 26 and Android M3.
---

# DartNative Bottom Navigation Bar Guide

DartNative's `BottomNavigationBar` compiles directly to native platform tab bars (`UITabBar` / `UITabBarController` on iOS and `BottomNavigationView` / M3 navigation bar on Android). Font glyphs on `Icon` widgets are rendered directly on the native tab items.

## Basic Tab Bar Usage

```dart
import 'package:dartnative/dartnative.dart';

int _selectedTab = 0;

Widget buildTabBar(BuildContext context) {
  return BottomNavigationBar(
    currentIndex: _selectedTab,
    onTap: (index) {
      // Update selected tab state
      _selectedTab = index;
    },
    items: const [
      BottomNavigationBarItem(
        label: 'Home',
        icon: Icon(CupertinoIcons.house),
        activeIcon: Icon(CupertinoIcons.house_fill),
      ),
      BottomNavigationBarItem(
        label: 'Settings',
        icon: Icon(CupertinoIcons.gear),
        activeIcon: Icon(CupertinoIcons.gear_fill),
      ),
    ],
  );
}
```

## Native Search Destinations (`BottomNavigationBarItem.search`)

On iOS 26+, adding a search item renders as a separated search pill (`UISearchTab`) alongside the tab bar:

```dart
BottomNavigationBar(
  currentIndex: _selectedTab,
  onTap: (i) => _selectedTab = i,
  onSearchQueryChanged: (query) {
    print('Search query changed: $query');
  },
  items: const [
    BottomNavigationBarItem(
      label: 'Home',
      icon: Icon(CupertinoIcons.house),
    ),
    // Dedicated native search destination
    BottomNavigationBarItem.search(
      label: 'Search',
      automaticallyActivatesSearch: true,
    ),
  ],
)
```

## Scroll Behavior & Controller Lowering (`TabBarScrollBehavior`)

Control how floating tab bars react to content scrolling inside an `IndexedStack`:

- **`none`** (Default): Standalone bar lowering.
- **`minimizeOnScrollDown`**: On iOS 26, floating pill minimizes on scroll down and restores on scroll up (`UITabBarController.tabBarMinimizeBehavior = .onScrollDown`). On Android, maps to M3 hide-on-scroll `BottomNavigationView`.
- **`minimizeOnScrollUp`**: Minimizes on scroll UP instead.

```dart
BottomNavigationBar(
  currentIndex: _selectedTab,
  onTap: (i) => _selectedTab = i,
  scrollBehavior: TabBarScrollBehavior.minimizeOnScrollDown,
  items: const [
    BottomNavigationBarItem(label: 'Feed', icon: Icon(CupertinoIcons.news)),
    BottomNavigationBarItem(label: 'Profile', icon: Icon(CupertinoIcons.person)),
  ],
)
```

## Styling Tab Items & Colors

Customize tab bar backgrounds, active indicators, and text/icon colors:

```dart
BottomNavigationBar(
  currentIndex: _selectedTab,
  onTap: (i) => _selectedTab = i,
  backgroundColor: Colors.black,
  indicatorColor: Colors.blueAccent, // M3 active indicator / iOS selection tint
  iconColor: Colors.grey,            // Unselected icon color
  selectedIconColor: Colors.white,   // Selected icon color
  labelFontStyle: const TextStyle(fontSize: 12.0, color: Colors.grey),
  selectedLabelFontStyle: const TextStyle(fontSize: 12.0, color: Colors.white),
  items: const [
    BottomNavigationBarItem(
      label: 'Home',
      subtitle: 'Dashboard', // iOS 26 subtitle line
      icon: Icon(CupertinoIcons.house),
    ),
  ],
)
```

## Gotchas

- **Search Tab Opt-In:** `BottomNavigationBarItem.search` opts into native `UITabBarController` lowering on iOS 26; on pre-26 iOS and Android, it renders as a standard tab with a native search icon.
- **Scroll Clearance:** On Android with `minimizeOnScrollDown`, reserve bottom clearance (~108dp) for edge-to-edge content layout.
- **Disabled Tabs:** Setting `enabled: false` on a `BottomNavigationBarItem` disables interaction and dims the tab on both standalone bars and controller lowerings across iOS and Android.
