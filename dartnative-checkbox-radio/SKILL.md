---
name: dartnative-checkbox-radio
description: Configure native selection controls in DartNative using Checkbox and Radio. Use when creating single or tristate checkboxes, radio button selection groups, customizing active colors, or mapping selection controls to native iOS (UIButton + SF Symbols) and Android controls.
---

# DartNative Checkbox & Radio Selection Controls Guide

DartNative provides native-backed selection controls (`Checkbox` and `Radio<T>`) backed by platform primitives (`UIButton` with SF Symbols on iOS, native Android controls).

## Checkbox (`Checkbox`)

Renders a native check box control with optional tristate (indeterminate) support:

```dart
import 'package:dartnative/dartnative.dart';

bool? _isChecked = false;

Widget buildCheckbox(BuildContext context) {
  return Checkbox(
    value: _isChecked,
    tristate: true, // Allows null (indeterminate: minus.square.fill)
    activeColor: Colors.blue,
    onChanged: (newValue) {
      // newValue can be true, false, or null (if tristate)
      _isChecked = newValue;
    },
  );
}
```

## Radio Button Groups (`Radio<T>`)

Renders a native radio button. A radio is selected when its `value` matches `groupValue`:

```dart
String _selectedOption = 'option1';

Widget buildRadioGroup(BuildContext context) {
  return Column(
    children: [
      ListTile(
        title: const Text('Option 1'),
        leading: Radio<String>(
          value: 'option1',
          groupValue: _selectedOption,
          activeColor: Colors.blue,
          onChanged: (val) {
            if (val != null) _selectedOption = val;
          },
        ),
      ),
      ListTile(
        title: const Text('Option 2'),
        leading: Radio<String>(
          value: 'option2',
          groupValue: _selectedOption,
          activeColor: Colors.blue,
          onChanged: (val) {
            if (val != null) _selectedOption = val;
          },
        ),
      ),
    ],
  );
}
```

## Gotchas

- **Tristate Indeterminate State:** When `tristate: true` and `value: null`, `Checkbox` displays the indeterminate state (`minus.square.fill` on iOS).
- **iOS SF Symbol SF Backing:** On iOS, `Checkbox` uses `checkmark.square.fill` / `square` / `minus.square.fill`, while `Radio` uses `circle.fill` / `circle`. The `checkColor` parameter is ignored on iOS because SF Symbols handle glyph tinting automatically.
- **Active Fill Color:** Setting `activeColor` customizes the selected fill tint across both `Checkbox` and `Radio`.
