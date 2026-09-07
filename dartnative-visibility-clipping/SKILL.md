---
name: dartnative-visibility-clipping
description: Guide for Visibility, Opacity, AnimatedOpacity, ClipRect, ClipRRect, and ClipRSuperellipse in DartNative.
---

# DartNative Visibility & Clipping Widgets

DartNative provides visibility toggles (`Visibility`, `Opacity`, `AnimatedOpacity`, `Offstage`) and native layer clipping (`ClipRect`, `ClipRRect`, `ClipRSuperellipse`).

---

## 1. Visibility & Offstage

Controls subtree mounting and native view visibility.

```dart
// Default: unmounts child subtree when visible == false
Visibility(
  visible: _show,
  replacement: const Text('Hidden'),
  child: MyWidget(),
)

// Maintain State: keeps child mounted and sets UIView.isHidden / View.INVISIBLE
Visibility(
  visible: _show,
  maintainState: true,
  child: MyWidget(),
)
```

- **`maintainState: true`**: Keeps child mounted and hides it natively (`SetViewHidden`). Tickers (e.g. `CanvasSurface`) auto-pause while hidden.

---

## 2. Opacity & AnimatedOpacity

Controls layer transparency (`UIView.alpha` on iOS).

```dart
AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  duration: const Duration(milliseconds: 300),
  curve: Curves.easeInOut,
  child: MyWidget(),
)
```

---

## 3. Clipping Widgets

Clips child content using CALayer masks and corner radii.

```dart
// Standard rectangular clip
ClipRect(child: MyWidget())

// Rounded rectangle clip (backed by layer.cornerRadius + masksToBounds)
ClipRRect(
  borderRadius: BorderRadius.circular(16),
  child: Image.asset('assets/avatar.png'),
)

// iOS Continuous Squircle / Superellipse clip
ClipRSuperellipse(
  borderRadius: BorderRadius.circular(16),
  child: MyWidget(),
)
```

---

## Platform Compatibility Matrix

| Widget | iOS Layer Backend | Android Backend |
|---|---|---|
| `Visibility(maintainState: true)` | `UIView.isHidden = true` | `View.INVISIBLE` |
| `ClipRRect` | `layer.cornerRadius` + `masksToBounds` | View Outline Clip |
| `ClipRSuperellipse` | `layer.cornerCurve = .continuous` | Superellipse Outline |
