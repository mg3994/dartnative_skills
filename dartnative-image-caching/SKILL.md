---
name: dartnative-image-caching
description: Display network, asset, file, and memory images in DartNative using Image, ImageProvider, ImageCache, and precacheImage. Use when loading images, setting ImageCachePolicy, downsampling decode dimensions with cacheWidth/cacheHeight to prevent OOMs, configuring native cache limits, or precaching images.
---

# DartNative Image Loading & Caching Guide

DartNative's `Image` widget wraps native platform image views (`UIImageView` with SDWebImage/CoreAnimation on iOS; Glide/Coil on Android), offering two-layer (memory + disk) native caching and downsampling.

## Displaying Images (`Image`)

Load network, asset, local file, or in-memory byte images:

```dart
import 'package:dartnative/dartnative.dart';

// Network image with placeholder and downsampling
Image.network(
  'https://example.com/photo.jpg',
  width: 200.0,
  height: 200.0,
  fit: BoxFit.cover,
  cacheWidth: 400, // Downsamples decode dimensions to cell pixel size
  cacheHeight: 400,
  placeholder: const ShimmerPlaceholder(),
  errorWidget: const Icon(CupertinoIcons.exclamationmark_triangle),
  fadeInDuration: const Duration(milliseconds: 200),
)

// Asset image
Image.asset('assets/logo.png', width: 48.0, height: 48.0)

// Memory image
Image.memory(bytes, fit: BoxFit.contain)
```

## Downsampling to Prevent OOMs (`cacheWidth` & `cacheHeight`)

A decoded bitmap costs `width × height × 4` bytes. Decoding full-resolution source images inside small list cells or grid tiles causes high memory overhead and potential OOMs.

Pass `cacheWidth` and `cacheHeight` (the cell's target pixel dimensions) to downsample the image during native decoding:

```dart
// Downsamples a 12MB source photo to a ~160KB cell bitmap
Image.network(
  item.photoUrl,
  cacheWidth: 300,
  cacheHeight: 300,
  fit: BoxFit.cover,
)
```

## Native Cache Policies (`ImageCachePolicy`)

Control caching behavior layer by layer:

- **`ImageCachePolicy.standard`** (Default): Caches in both native memory (`NSCache`) and on disk (`URLCache`).
- **`ImageCachePolicy.memoryOnly`**: Caches in memory only (not persisted to disk). Ideal for ephemeral or authenticated sensitive content.
- **`ImageCachePolicy.none`**: Bypasses all caches; re-downloads fresh content every time.

```dart
Image.network(
  'https://example.com/live-feed.jpg',
  cachePolicy: ImageCachePolicy.none, // Always fresh
)
```

## Configuring & Clearing `ImageCache`

Configure native memory and disk cache limits once at startup before `runApp`:

```dart
void main() {
  // Configure global native image cache limits
  ImageCache.configure(
    maxMemoryBytes: 100 * 1024 * 1024, // 100 MB
    maxDiskBytes: 200 * 1024 * 1024,   // 200 MB
    trimRatio: 0.5,                    // Evict oldest 50% when full
  );

  runApp(const MyApp());
}

// Programmatic eviction and queries
if (ImageCache.isCached(imageUrl)) {
  print('Image is in memory');
}

// Evict a specific URL or clear cache layers
ImageCache.evict(NetworkImage(imageUrl));
ImageCache.clearMemory();
ImageCache.clearAll();
```

## Precaching Images (`precacheImage`)

Pre-download images into native caches before displaying them in UI:

```dart
@override
void initState() {
  super.initState();
  // Pre-download hero image into native cache
  precacheImage(NetworkImage('https://example.com/hero.jpg'));
}
```

## Gotchas

- **Instant Memory Hits:** Memory-cache hits automatically skip `fadeInDuration` and `placeholder`—the image renders instantly.
- **Single Dimension Downsampling:** When only one of `cacheWidth` or `cacheHeight` is provided, the native decoder scales the unspecified dimension to preserve the original aspect ratio.
- **SHA-256 Cache Keys:** Native cache keys are SHA-256 hashes of the URL string, making them safe for long query parameters or token-bearing network paths.
- **Import Aliasing:** Always import UI types from `package:dartnative/dartnative.dart` or `package:dartnative/canvas.dart as ui` (never `dart:ui`).
