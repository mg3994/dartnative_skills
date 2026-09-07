---
name: dartnative-native-pickers
description: Comprehensive guide for presenting native color, date, and media pickers in DartNative using showColorPicker, showDatePicker, and showMediaPicker.
---

# DartNative Native Pickers

DartNative provides imperative APIs to present platform-native color, date/time, and media selection dialogs.

---

## 1. Color Picker (`showColorPicker`)

Presents a native system color picker.

```dart
Future<Color?> showColorPicker({
  required BuildContext context,
  Color initialColor = const Color(0xFF0A84FF),
  bool supportsAlpha = true,
  String title = '',
  ValueChanged<Color>? onChanged,
})
```

- **`context`**: A mounted `BuildContext`.
- **`initialColor`**: Starting selected color (defaults to `0xFF0A84FF`).
- **`supportsAlpha`**: Whether opacity/alpha selection is enabled.
- **`onChanged`**: Optional live callback fired as the user adjusts the color.
- **Return Value**: `Future<Color?>` returning the picked color or `null` if cancelled.

---

## 2. Date Picker (`showDatePicker`)

Presents a native date or time picker (`UIDatePicker` on iOS).

```dart
Future<DateTime?> showDatePicker({
  required BuildContext context,
  NativeDatePickerMode mode = NativeDatePickerMode.date,
})
```

### Modes (`NativeDatePickerMode`)
- `date`: Pick year, month, and day.
- `time`: Pick hours and minutes.
- `dateAndTime`: Combined date and time selection.
- `countDownTimer`: Countdown timer duration.

- **Return Value**: `Future<DateTime?>` returning the chosen `DateTime`, or `null` if dismissed/cancelled (matches Flutter's `showDatePicker` contract).
- **Difference from Flutter**: Uses pure OS controls; range parameters (`firstDate`/`lastDate`) are omitted.

---

## 3. Media Picker (`showMediaPicker`)

Presents a native photo/video picker (`PHPickerViewController` / `UIImagePickerController` on iOS).

```dart
Future<List<MediaFile>> showMediaPicker({
  required BuildContext context,
  MediaPickerType type = MediaPickerType.images,
  int maxSelection = 1,
})
```

### Media Types (`MediaPickerType`)
- `images`: Image files only.
- `videos`: Video files only.
- `imagesAndVideos`: Both images and videos.

### Selected Item (`MediaFile`)
- **`path`**: Absolute path to the picked file in the app's temp directory.
- **`name`**: Original file name (e.g. `"IMG_0042.HEIC"`).
- **`type`**: `"image"` or `"video"`.

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

void pickUserAvatar(BuildContext context) async {
  // 1. Pick an image file
  final files = await showMediaPicker(
    context: context,
    type: MediaPickerType.images,
    maxSelection: 1,
  );

  if (files.isNotEmpty) {
    print('Picked image path: ${files.first.path}');
  }

  // 2. Pick a birthdate
  final date = await showDatePicker(
    context: context,
    mode: NativeDatePickerMode.date,
  );

  if (date != null) {
    print('Selected date: $date');
  }

  // 3. Pick a theme color
  final color = await showColorPicker(
    context: context,
    initialColor: Colors.blue,
  );

  if (color != null) {
    print('Selected color: $color');
  }
}
```

---

## Platform Compatibility Matrix

| API | iOS Implementation | Android / Fallback |
|---|---|---|
| `showColorPicker` | System Color Sampler / `UIColorPickerViewController` | Material Color Picker |
| `showDatePicker` | `UIDatePicker` | Material Date/Time Dialog |
| `showMediaPicker` | `PHPickerViewController` (iOS 14+) / `UIImagePickerController` | Android Photo Picker |
