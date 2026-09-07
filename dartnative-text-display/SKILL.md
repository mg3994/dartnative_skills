---
name: dartnative-text-display
description: Guide for Text, RichText, TextSpan, and TapGestureRecognizer in DartNative, backed by UILabel, TextView, and NSAttributedString.
---

# DartNative Text Display Widgets

`Text` and `RichText` render plain and styled attributed text using native platform views (`UILabel` / `TextView` on iOS).

---

## 1. Text Widget

Displays a single string run.

```dart
Text(
  'Hello DartNative',
  style: TextStyle(fontSize: 18, color: Colors.black),
  textAlign: TextAlign.center,
  maxLines: 2,
  overflow: TextOverflow.ellipsis,
  selectable: true,   // Swaps backing view to read-only UITextView / EditText for long-press copy
  detectLinks: true,  // Detects URLs/phone numbers automatically (UITextView dataDetectorTypes)
)
```

- **`selectable: true`**: Swaps backing view from `UILabel` to `UITextView` (iOS) / `EditText` (Android) to enable long-press text selection and copy.
- **`detectLinks: true`**: Automatically highlights URLs and phone numbers for click handling.

---

## 2. RichText & TextSpan

Renders nested attributed text trees (`NSAttributedString` on iOS).

```dart
RichText(
  text: TextSpan(
    text: 'By signing up, you agree to ',
    style: const TextStyle(color: Colors.black, fontSize: 14),
    children: [
      TextSpan(
        text: 'Terms & Conditions',
        style: const TextStyle(color: Colors.blue, fontWeight: FontWeight.bold),
        recognizer: TapGestureRecognizer()
          ..onTap = () {
            print('Terms tapped!');
          },
      ),
    ],
  ),
)
```

> **System Font Gotcha**: Dotted system font names (e.g. `.SF Pro Text`) fall back to serif in `RichText`. Omit `fontFamily` in `TextStyle` to maintain the default system font.

---

## Platform Compatibility Matrix

| Widget | Default iOS View | Selectable View |
|---|---|---|
| `Text` | `UILabel` | Read-only `UITextView` |
| `RichText` | `UILabel` (with `NSAttributedString`) | Tappable spans via `TapGestureRecognizer` |
