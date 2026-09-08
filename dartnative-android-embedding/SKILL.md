---
name: dartnative-android-embedding
description: Guide for Android FlutterPlugin embedding, DNAppContext application/activity tracking, DNCoilHelper image loading, and DNAndroidPluginProvider in DartNative.
---

# DartNative Android Embedding & Plugin Provider

The `dartnative_android` native library initializes image loaders, registers global Android Application/Activity references, loads native Skia binaries, and provides mutation handling for native views.

---

## 1. Engine Attachment (`DartNativeAndroidPlugin`)

`DartNativeAndroidPlugin` implements Flutter's `FlutterPlugin` interface:

```java
public final class DartNativeAndroidPlugin implements FlutterPlugin {
    @Override
    public void onAttachedToEngine(FlutterPluginBinding binding) {
        // 1. Initialize Facebook SoLoader
        SoLoader.init(binding.getApplicationContext(), false);

        // 2. Register global Application context
        Context context = binding.getApplicationContext();
        if (context instanceof Application) {
            DNAppContext.set((Application) context);
        }

        // 3. Initialize Coil image loader
        DNCoilHelper.init(context);

        // 4. Load native Skia engine library
        try {
            System.loadLibrary("dartnative_skia");
        } catch (UnsatisfiedLinkError e) {
            // Skia library optional depending on build tier
        }
    }

    @Override
    public void onDetachedFromEngine(FlutterPluginBinding binding) {}
}
```

---

## 2. Global Context & Activity Tracking (`DNAppContext`)

`DNAppContext` maintains thread-safe global references to the Android `Application` context and current active `Activity` via weak references and lifecycle callbacks:

```kotlin
// Access global Application context
val context: Context? = DNAppContext.get()

// Access currently active Foreground Activity
val currentActivity: Activity? = DNAppContext.activity()
```

### Lifecycle Monitoring
- Automatically attaches `ActivityLifecycleCallbacks` when `DNAppContext.set(application)` is called.
- Updates a `WeakReference<Activity>` on `onActivityStarted` / `onActivityResumed`.
- Clears the reference when the active activity is stopped or destroyed.

---

## 3. Native Plugin View Provider (`DNAndroidPluginProvider`)

Interface implemented by Android native plugins hosting native Android views inside DartNative:

```java
public interface DNAndroidPluginProvider {
    // Create native Android View by type index
    @Nullable
    View createView(int typeIndex);

    // Handle view mutations dispatched from Dart FFI reconciler
    void handleMutation(long viewId, int eventTag, @NotNull byte[] data);
}
```

---

## Key Integration Patterns

1. **Native View Creation**: `createView(typeIndex)` is invoked when a `NativeElement` is mounted on the Dart side.
2. **Byte-Buffer Mutations**: `handleMutation(viewId, eventTag, data)` decodes binary mutation payloads sent directly from the Dart reconciler over FFI without Flutter Channel overhead.

---

## Platform Summary Matrix

| Component | Responsibility | Scope |
|---|---|---|
| `DartNativeAndroidPlugin` | SoLoader, Coil, `dartnative_skia` init | Engine Attachment |
| `DNAppContext` | Global `Context` & `WeakReference<Activity>` | App Lifecycle |
| `DNAndroidPluginProvider` | Native `View` creation & byte mutation dispatch | View Hosting |
