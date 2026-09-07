---
name: dartnative-scaffold-appbar
description: Detailed guide for building top-level UI structures using Scaffold, AppBar, BackButton, BarButtonItem, Drawer, SnackBar, and showToast in DartNative.
---

# DartNative Scaffold & AppBar

`Scaffold` and `AppBar` provide the primary screen structure in DartNative, backed natively by `UIViewController` and `UINavigationBar`.

---

## 1. Scaffold

Top-level page wrapper.

```dart
Scaffold({
  Widget? body,
  PreferredSizeWidget? appBar,
  Widget? bottomNavigationBar,
  Widget? floatingActionButton,
  Color? backgroundColor,
  bool extendBodyBehindAppBar = false, // Enables iOS 26 Liquid Glass blur behind bar
  bool extendBody = false,             // Enables content behind floating bottom pill
  Brightness? brightness,              // Forces dark/light interface mode on screen
  Widget? bottomInputBar,              // Chat input bar moving with keyboard
  Widget? bottomAccessory,            // Apple Music-style now-playing accessory
  Widget? drawer,                     // Reveal/push side panel
  DrawerStyle drawerStyle = DrawerStyle.push,
})
```

### Key Capabilities
- **`extendBodyBehindAppBar`**: Essential for iOS 26 Liquid Glass frosted navigation bars.
- **`bottomInputBar`**: Pinned input bar that smoothly lifts and dismisses interactively with the software keyboard.
- **`bottomAccessory`**: Lowers to native `UITabAccessory` on iOS 26 when combined with tab bars.
- **`drawer`**: Reveals a `Drawer` using push or slide-over transitions (`Scaffold.of(context).openDrawer()`).

---

## 2. AppBar

Material/iOS navigation toolbar backed by `UINavigationBar`.

```dart
AppBar({
  Widget? leading,
  Widget? title,
  List<Widget>? actions,
  Color? backgroundColor,
  bool automaticallyImplyLeading = true,
  bool showBackTitle = false,          // Show "‹ Previous Title" back button text
  double? toolbarHeight,               // 44pt standard, 52pt iOS 26
  Widget? subtitle,                   // Secondary presence/status line
  Widget? largeTitle,                 // Collapsing iOS/M3 headline
  SearchBar? searchBar,               // M3 Search Bar / UISearchBar integration
  AppBarIOSConfig? ios,               // Controls systemBar cross-screen button morphing
})
```

### Bar Buttons (`BarButtonItem` & `BackButton`)
- **`BackButton`**: Customizes the back chevron glyph, text label, tint, and iOS 26 glass capsule.
- **`BarButtonItem`**: Defines trailing actions or native context menus (`menu: [MenuAction(...)]`).

---

## 3. Drawers (`Drawer` & `DrawerHeader`)

Side navigation panel sliding in from the leading edge.

```dart
Scaffold(
  drawer: Drawer(
    width: 304,
    child: ListView(
      children: [
        DrawerHeader(child: Text('Menu Header')),
        ListTile(title: Text('Home')),
      ],
    ),
  ),
  body: MyContent(),
)
```

---

## 4. Toasts & SnackBars (`showToast` / `SnackBar`)

Transient notification overlays.

```dart
// Imperative Toast Pill
showToast(
  context,
  'Message Copied',
  position: ToastPosition.top,
  iconName: 'doc.on.doc',
);

// Scaffold Messenger SnackBar
ScaffoldMessenger.of(context).showSnackBar(
  const SnackBar(content: Text('Item saved to library')),
);
```

---

## Complete Example

```dart
import 'package:dartnative/dartnative.dart';

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      extendBodyBehindAppBar: true,
      appBar: AppBar(
        title: const Text('Dashboard'),
        actions: [
          BarButtonItem(
            fontIcon: MaterialSymbolsRounded.settings,
            onPressed: () {},
          ),
        ],
      ),
      drawer: Drawer(
        child: ListView(
          children: const [
            DrawerHeader(child: Text('Navigation')),
          ],
        ),
      ),
      body: Center(
        child: ElevatedButton(
          onPressed: () => showToast(context, 'Hello DartNative!'),
          child: const Text('Show Toast'),
        ),
      ),
    );
  }
}
```

---

## Platform Compatibility Matrix

| Feature | iOS Implementation | Android Implementation |
|---|---|---|
| `Scaffold` | `UIViewController` | Root ViewGroup |
| `AppBar` | `UINavigationBar` | Material 3 `TopAppBar` |
| `showToast` | Liquid Glass Toast Pill | Native Toast |
