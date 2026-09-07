---
name: dartnative-cupertino-icons
description: Look up and display Cupertino icon glyphs by SF Symbol name in DartNative using kCupertinoIconsByName and cupertinoIconForSymbol. Use when dynamically loading Cupertino icons, mapping string symbol names to IconData, or displaying iOS-styled glyphs.
---

# DartNative Cupertino Icon Lookup Guide

DartNative provides the `kCupertinoIconsByName` lookup table and `cupertinoIconForSymbol()` function to dynamically map SF Symbol string names to `IconData` constants from `CupertinoIcons`.

## Dynamic Icon Lookup by Symbol Name

Look up `IconData` by SF Symbol string name:

```dart
import 'package:dartnative/dartnative.dart';

// Look up IconData dynamically from SF Symbol string name
IconData? getIcon(String symbolName) {
  return kCupertinoIconsByName[symbolName] ?? cupertinoIconForSymbol(symbolName);
}

// Display in an Icon widget
Widget buildIcon(String symbol) {
  final iconData = getIcon(symbol) ?? CupertinoIcons.question;
  return Icon(iconData, size: 24.0, color: Colors.blue);
}
```

## Common Cupertino Icon Names (`kCupertinoIconsByName`)

Common SF Symbol names mapped in `kCupertinoIconsByName`:

- **Navigation:** `'chevron_left'`, `'chevron_right'`, `'chevron_back'`, `'chevron_forward'`, `'arrow_left'`, `'arrow_right'`
- **Actions:** `'pencil'`, `'trash'`, `'plus'`, `'minus'`, `'xmark'`, `'checkmark'`, `'search'`, `'share'`, `'square_arrow_up'`
- **Status & Controls:** `'heart'`, `'heart_fill'`, `'star'`, `'star_fill'`, `'bell'`, `'bell_fill'`, `'gear'`, `'lock'`, `'unlock'`
- **Media & Devices:** `'play'`, `'play_fill'`, `'pause'`, `'pause_fill'`, `'speaker_fill'`, `'camera'`, `'camera_fill'`, `'phone'`, `'phone_fill'`
- **Objects & Users:** `'person'`, `'person_fill'`, `'person_2'`, `'folder'`, `'envelope'`, `'envelope_fill'`, `'house'`, `'house_fill'`

## Usage in Buttons & Navigation

Use string lookup when configuring dynamic bar items or button icons:

```dart
// Dynamic Icon creation from string configuration
String currentSymbol = 'bell_fill';

IconButton(
  icon: Icon(kCupertinoIconsByName[currentSymbol] ?? CupertinoIcons.bell),
  onPressed: () {
    print('Notification tapped');
  },
)
```

## Gotchas

- **Null Safety Fallback:** When looking up icons dynamically using `kCupertinoIconsByName[name]`, always provide a fallback `IconData` (such as `CupertinoIcons.question` or `CupertinoIcons.exclamationmark`) in case an unmapped symbol string is supplied.
- **Font Glyph Backing:** `CupertinoIcons` map directly to Cupertino font glyph code points and render as native `UIImageView` glyphs on iOS and Android without extra asset bundle overhead.
