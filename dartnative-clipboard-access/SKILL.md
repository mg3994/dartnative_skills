---
name: dartnative-clipboard-access
description: Read and write system clipboard text in DartNative using Clipboard and ClipboardData. Use when copying text to UIPasteboard/Android clipboard or reading clipboard content with proper platform permission notice awareness.
---

# DartNative Clipboard Access Guide

DartNative provides access to the system clipboard (`UIPasteboard.general` on iOS and system ClipboardManager on Android) via the `Clipboard` utility class, maintaining Flutter API compatibility.

## Writing Text to Clipboard (`Clipboard.setData`)

Writes plain text to the system pasteboard:

```dart
import 'package:dartnative/dartnative.dart';

Future<void> copyMessageText(String messageText) async {
  await Clipboard.setData(ClipboardData(text: messageText));
  print('Text copied to system clipboard.');
}
```

## Reading Text from Clipboard (`Clipboard.getData`)

Reads plain text from the system pasteboard:

```dart
Future<void> pasteFromClipboard() async {
  final data = await Clipboard.getData(Clipboard.kTextPlain);
  if (data != null && data.text.isNotEmpty) {
    print('Pasted text: ${data.text}');
  } else {
    print('Clipboard is empty or contains non-text content.');
  }
}
```

## Gotchas & Platform Permissions

- **iOS Privacy Banners:** Reading the clipboard via `Clipboard.getData()` triggers iOS system privacy alerts (e.g. *"App pasted from Safari"*) and permission prompts. Only invoke `Clipboard.getData()` in direct response to user-initiated actions (such as tapping a Paste button), never automatically or in background loops.
- **Supported Formats:** Plain text (`Clipboard.kTextPlain`) is the primary supported format.
- **Synchronous FFI Write:** Although `Clipboard.setData()` returns a `Future<void>` to match Flutter's signature, the underlying FFI call to `UIPasteboard` is synchronous and instantaneous.
