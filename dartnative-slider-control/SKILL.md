---
name: dartnative-slider-control
description: Guide for Slider and SliderTheme in DartNative, mapping to UISlider on iOS and SeekBar / Material 3 Slider on Android.
---

# DartNative Slider & SliderTheme

`Slider` provides a continuous or discrete value selector backed by `UISlider` on iOS and `SeekBar` / Material 3 `Slider` on Android.

---

## Key Characteristics & API

```dart
Slider({
  required double value,
  ValueChanged<double>? onChanged,     // Fired continuously while dragging
  ValueChanged<double>? onChangeStart, // Fired when drag starts
  ValueChanged<double>? onChangeEnd,   // Fired when drag releases
  double min = 0,
  double max = 1,
  int? divisions,                     // Snaps to discrete intervals
  Color? activeColor,
  Color? inactiveColor,
  Color? thumbColor,
  AndroidSliderStyle? android,        // Material 3 slider styling on Android
})
```

---

## Slider Theme Customization

Wrap `Slider` in a `SliderTheme` to customize track height, colors, and thumb/overlay shapes across descendant sliders:

```dart
SliderTheme(
  data: SliderThemeData(
    trackHeight: 6.0,
    activeTrackColor: Colors.blue,
    inactiveTrackColor: kSliderDefaultInactiveTrackColor,
    thumbColor: Colors.blueAccent,
  ),
  child: Slider(
    value: _val,
    onChanged: (v) => setState(() => _val = v),
  ),
)
```

---

## Android Material 3 Styling (`AndroidSliderStyle`)

Passing `AndroidSliderStyle` switches the Android backing view to Material 3's Slider anatomy:

```dart
Slider(
  value: _val,
  onChanged: (v) => setState(() => _val = v),
  android: const AndroidSliderStyle(
    trackHeight: 12,
    thumbWidth: 6,
    thumbTrackGap: 4,
  ),
)
```

---

## Example Usage

```dart
import 'package:dartnative/dartnative.dart';

Widget buildVolumeControl(double volume, ValueChanged<double> onVolumeChanged) {
  return Row(
    children: [
      const Icon(CupertinoIcons.volume_down),
      Expanded(
        child: Slider(
          value: volume,
          min: 0.0,
          max: 1.0,
          activeColor: Colors.blue,
          onChanged: onVolumeChanged,
        ),
      ),
      const Icon(CupertinoIcons.volume_up),
    ],
  );
}
```

---

## Platform Compatibility Matrix

| Platform | Standard View | Material 3 Style |
|---|---|---|
| **iOS** | `UISlider` | Native HIG `UISlider` |
| **Android** | `SeekBar` | Material 3 `Slider` (via `android:` prop) |
