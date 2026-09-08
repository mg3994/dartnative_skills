---
name: dartnative-android-components
description: Guide for Android native backdrop blur (DNBackdropFilterView), text baseline shifting (DNBaselineShiftSpan), and splash screen boot gate transitions (DNBootGate) in DartNative.
---

# DartNative Android Components

The `dartnative_android` native library includes specialized views, text spans, and startup transition handlers to deliver hardware-accelerated UI effects on Android.

---

## 1. Native Backdrop Blur (`DNBackdropFilterView`)

`DNBackdropFilterView` provides hardware-accelerated real-time backdrop blur on Android API 31+ (Android 12+) using `RenderNode` and `RenderEffect.createBlurEffect`.

```kotlin
// Native Backdrop Blur Control
val backdropView = DNBackdropFilterView(context)
backdropView.setBackdropBlur(sigmaX = 15f, sigmaY = 15f)
```

### Android API 31+ Mechanism
1. **Target View Resolution**: Resolves the ancestor `DNView` target and excludes overlapping content during blur recording (`acquireBackdropTarget()`).
2. **`RenderNode` Blur Recording**: Offscreens the backdrop target to a hardware `RenderNode` using `RenderEffect.createBlurEffect(sigmaX, sigmaY, TileMode.CLAMP)`.
3. **Screen Offset Translation**: Computes global screen coordinate deltas (`dx`, `dy`) between `DNBackdropFilterView` and its backdrop target to align blur sampling perfectly.
4. **Older Android SDKs (< API 31)**: Gracefully passes through rendering without blur (no crash).

---

## 2. Text Baseline Shift Span (`DNBaselineShiftSpan`)

`DNBaselineShiftSpan` shifts inline text glyphs above or below the baseline on Android `TextView` / `SpannableString`:

```kotlin
class DNBaselineShiftSpan(val shiftPx: Int) : CharacterStyle() {
    override fun updateDrawState(tp: TextPaint) {
        tp.baselineShift += shiftPx
    }
}
```

- **`shiftPx > 0`**: Raises text glyphs above baseline.
- **`shiftPx < 0`**: Lowers text glyphs below baseline.
- **Usage**: Used internally when bridging `TextStyle.baselineOffset` from Dart to Android `SpannableString`.

---

## 3. Splash Screen Boot Gate (`DNBootGate`)

`DNBootGate` manages seamless splash screen exit transitions to prevent white frame flashes during initial engine startup.

```kotlin
// Install splash transition gate on Activity startup
DNBootGate.install(activity)

// Surface lifecycle notifications
DNBootGate.noteSurfaceCreated()
DNBootGate.noteSurfaceFramePresented()
```

### Boot Gate Logic
1. **API 31+ Android Splash Screen**: Captures `SplashScreenView` pixels into a temporary bitmap and places a full-bleed cover over `decorView`.
2. **Pre-API 31**: Captures `launch_background` drawable and places a placeholder `FrameLayout` cover.
3. **Ready Condition**: Holds the cover until:
   - Minimum 200ms elapsed.
   - Surface frame presented (`surfaceFramePresented == true`).
   - Insets seeded (`DNSafeArea.getSeeded() == true`).
4. **Smooth Exit**: Fades out the cover with a 150ms alpha animation once ready.

---

## Platform Summary Matrix

| Component | Class | Android Version Requirements |
|---|---|---|
| Backdrop Blur | `DNBackdropFilterView` | API 31+ (Android 12+) |
| Baseline Shift | `DNBaselineShiftSpan` | All Android versions |
| Startup Gate | `DNBootGate` | API 31+ SplashScreen & Legacy Drawables |
