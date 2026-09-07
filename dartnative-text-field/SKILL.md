---
name: dartnative-text-field
description: Comprehensive guide for TextField in DartNative, covering native UITextField/UITextView backing, clearButtonMode, accessory icons, and Liquid Glass container integration.
---

# DartNative TextField

`TextField` provides single-line (`UITextField`) or multi-line (`UITextView`) text input with Flutter-compatible `InputDecoration` and native accessory view features.

---

## Key Characteristics & API

```dart
TextField({
  TextEditingController? controller,
  FocusNode? focusNode,
  InputDecoration decoration = const InputDecoration(),
  TextInputType keyboardType = TextInputType.text,
  TextInputAction? textInputAction,
  TextCapitalization textCapitalization = TextCapitalization.none,
  TextStyle? style,
  TextAlign textAlign = TextAlign.start,
  TextAlignVertical? textAlignVertical,
  int? maxLines = 1,
  int? minLines,
  int? maxLength,
  bool obscureText = false,
  bool autocorrect = true,
  bool autofocus = false,
  bool readOnly = false,
  bool enabled = true,
  Color? cursorColor,
  ValueChanged<String>? onChanged,
  void Function(String)? onSubmitted,

  // DartNative Accessory & Clear Button Extras
  IconData? prefixIcon,
  double prefixIconSize = 20,
  Color? prefixIconColor,
  EdgeInsets? prefixIconPadding,
  IconData? suffixIcon,
  double suffixIconSize = 20,
  Color? suffixIconColor,
  EdgeInsets? suffixIconPadding,
  ClearButtonMode clearButtonMode = ClearButtonMode.never,
  IconData? clearIcon,
  Color? clearIconColor,
})
```

---

## Accessory Views & Clear Button (`ClearButtonMode`)

Unlike Flutter where icons require custom `IconButton` wrappers, DartNative renders `prefixIcon` and `suffixIcon` as native `leftView` / `rightView` accessory views.

### Clear Button Modes (`ClearButtonMode`)
- `never`: No clear button (default).
- `always`: Always visible when field contains text.
- `whileEditing`: Visible when focused + text present.
- `unlessEditing`: Visible when unfocused + text present.

---

## Surface Styling & Liquid Glass

The `TextField` widget itself renders no background box. Wrap it in the appropriate container for your surface:

1. **Floating / Overlays (iOS 26 Liquid Glass)**:
   ```dart
   GlassEffectContainer(
     borderRadius: BorderRadius.circular(18),
     child: const TextField(
       decoration: InputDecoration(hintText: 'Search or type URL'),
       clearButtonMode: ClearButtonMode.whileEditing,
     ),
   )
   ```
2. **Flat Forms / Cards**:
   ```dart
   Container(
     decoration: BoxDecoration(
       color: Color(0xFFF2F2F7),
       borderRadius: BorderRadius.circular(8),
     ),
     child: const TextField(
       decoration: InputDecoration(hintText: 'Enter name'),
     ),
   )
   ```

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildSearchInput(TextEditingController controller) {
  return TextField(
    controller: controller,
    prefixIcon: MaterialSymbolsRounded.search,
    prefixIconColor: Colors.grey,
    clearButtonMode: ClearButtonMode.whileEditing,
    decoration: const InputDecoration(
      hintText: 'Search messages',
    ),
  );
}
```

---

## Platform Compatibility Matrix

| Feature | iOS Implementation | Android Implementation |
|---|---|---|
| Single-line Field | `UITextField` | `EditText` |
| Multi-line Field | `UITextView` | `EditText` (`multiline`) |
| Accessory Views | `leftView` / `rightView` | Native Start/End Compound Drawables |
| Clear Button | Native `UITextFieldViewMode` | Native Clear Icon |
