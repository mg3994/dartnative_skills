---
name: dartnative-search-bar
description: Guide for Material 3 and iOS native SearchBar in DartNative, featuring docked search pills and expanding search surfaces.
---

# DartNative SearchBar

`SearchBar` provides a search interface that lowers to native Material 3 `com.google.android.material.search` (`SearchBar` + `SearchView`) on Android and `UISearchBar` / `UISearchController` on iOS.

---

## Key Characteristics & API

```dart
SearchBar({
  String hintText = 'Search',          // Always include "Search" per M3 spec
  ValueChanged<String>? onChanged,     // Streams text as user types in expanded field
  ValueChanged<String>? onSubmitted,   // Fires on IME search action
  Widget? suggestions,                // Content of expanded search surface (suggestions/results)
  Color? backgroundColor,             // Fill of search pill/field
  Color? surfaceColor,                 // Background of expanded search surface
})
```

---

## Platform Lowering Behavior

### Android (`com.google.android.material.search`)
- Lowers to `SearchBar` (docked pill) + `SearchView` (expanding surface).
- Features native morph transition, back-arrow collapse, and keyboard handling.
- `suggestions` widget mounts inside the expanded surface and updates live.

### iOS (`UISearchController` & `UISearchBar`)
- Lowers to `UISearchBar` owned by a retained `UISearchController`.
- Tapping activates system search presentation over the screen with Liquid Glass styling on iOS 26.
- Inside `AppBar.searchBar`, the pill acts as the navigation bar's `titleView`.

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

class ContactSearchScreen extends StatefulWidget {
  const ContactSearchScreen({super.key});

  @override
  State<ContactSearchScreen> createState() => _ContactSearchScreenState();
}

class _ContactSearchScreenState extends State<ContactSearchScreen> {
  String _query = '';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        searchBar: SearchBar(
          hintText: 'Search contacts',
          onChanged: (q) => setState(() => _query = q),
          onSubmitted: (q) => print('Searching for $q'),
          suggestions: ListView(
            children: [
              ListTile(title: Text('Result for $_query')),
            ],
          ),
        ),
      ),
      body: const Center(child: Text('Main Content')),
    );
  }
}
```

---

## Platform Compatibility Matrix

| Feature | iOS Implementation | Android Implementation |
|---|---|---|
| Docked Field | `UISearchBar` (`titleView`) | Material 3 `SearchBar` pill |
| Expanded Surface | `UISearchController` results overlay | Material 3 `SearchView` |
