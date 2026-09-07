---
name: dartnative-native-alert
description: Detailed guide for displaying imperative native platform alert dialogs and action sheets in DartNative using showAlert and showActionSheet.
---

# DartNative Native Alerts & Action Sheets

DartNative provides imperative APIs to trigger platform-native modal dialogs (`UIAlertController` on iOS) directly from Dart code.

---

## Functions Reference

### `showAlert`

Presents a native modal alert dialog.

```dart
Future<int> showAlert({
  required BuildContext context,
  required String title,
  String message = '',
  List<String> actions = const ['OK'],
})
```

- **`context`**: A mounted `BuildContext`.
- **`title`**: Header title of the alert.
- **`message`**: Body description (optional).
- **`actions`**: Button labels. On iOS, the first action is treated as the default/cancel action.
- **Return Value**: `Future<int>` resolving to the 0-based index of the button tapped by the user.

---

### `showActionSheet`

Presents a native action sheet (bottom action list on iOS).

```dart
Future<int> showActionSheet({
  required BuildContext context,
  String title = '',
  String message = '',
  required List<String> actions,
  String cancelLabel = 'Cancel',
  List<int> destructiveIndices = const [],
})
```

- **`actions`**: List of options presented in the sheet.
- **`cancelLabel`**: Label for the cancel button (defaults to `'Cancel'`).
- **`destructiveIndices`**: List of action indices that should be rendered with destructive styling (e.g., red text).
- **Return Value**: `Future<int>` resolving to the tapped action index (0-based), or `-1` if cancelled.

---

## Basic Usage Example

```dart
import 'package:dartnative/dartnative.dart';

void handleDeleteItem(BuildContext context) async {
  final index = await showActionSheet(
    context: context,
    title: 'Delete Item',
    message: 'Are you sure you want to permanently delete this file?',
    actions: ['Delete', 'Archive'],
    cancelLabel: 'Cancel',
    destructiveIndices: [0], // 'Delete' action is highlighted in red
  );

  if (index == 0) {
    // Delete action chosen
    await showAlert(
      context: context,
      title: 'Success',
      message: 'Item has been deleted.',
      actions: ['OK'],
    );
  } else if (index == 1) {
    // Archive action chosen
  }
}
```

---

## Platform Compatibility Matrix

| API | iOS Implementation | Android / Fallback |
|---|---|---|
| `showAlert` | `UIAlertController` (`.alert` style) | Native Dialog |
| `showActionSheet` | `UIAlertController` (`.actionSheet` style) | Bottom Sheet Dialog |
