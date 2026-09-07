---
name: dartnative-android-text-decoration
description: Guide for Android native text decoration bridging in DartNative using the com.dartnative.ColouredDecoration Kotlin data class.
---

# DartNative Android Text Decoration (`ColouredDecoration`)

In the Android runtime of `dartnative` (`dartnative_android`), text span styling and decorations (underlines, background highlights, strikethrough) are represented on the native side by the `com.dartnative.ColouredDecoration` data class.

---

## Class Structure (`ColouredDecoration`)

Package: `com.dartnative`

```kotlin
internal data class ColouredDecoration(
    val start: Int,
    val end: Int,
    val type: Int,
    val argb: Int
)
```

---

## Parameter Definitions

1. **`start`** (`Int`): Starting character offset (0-based) in the text string for the decoration span.
2. **`end`** (`Int`): Ending character offset (exclusive) for the decoration span.
3. **`type`** (`Int`): Integer flag specifying the decoration type (e.g. underline, line-through, background fill).
4. **`argb`** (`Int`): Packed 32-bit ARGB color value applied to the decoration span.

---

## How It Works in `dartnative_android`

1. **FFI / JNI Bridge**: As attributed text spans (`TextSpan` / `RichText`) cross FFI / JNI into Android, `ColouredDecoration` instances are instantiated for decorated ranges.
2. **SpannableString Application**: The Android native layer iterates over `ColouredDecoration` items and applies appropriate Android `CharacterStyle` spans (e.g., `ForegroundColorSpan`, `BackgroundColorSpan`, `UnderlineSpan`) to the `SpannableString`.
3. **TextView Rendering**: The resulting `SpannableString` is set on the backing Android `TextView` for rendering.

---

## Usage Concept

When designing custom native plugins or FFI extensions that manipulate text decorations on Android:

```kotlin
// Example native instantiation in Kotlin/Java
val decoration = ColouredDecoration(
    start = 0,
    end = 12,
    type = 1, // Underline / Accent
    argb = 0xFF2196F3.toInt() // Blue color
)
```

---

## Platform Compatibility Matrix

| Property | Android Native Binding | iOS Equivalent |
|---|---|---|
| Range | `start` / `end` | `NSRange(location, length)` |
| Style & Color | `type` & `argb` | `NSAttributedString.Key` attributes |
