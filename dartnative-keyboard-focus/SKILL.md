---
name: dartnative-keyboard-focus
description: Manage keyboard focus and first-responder state in DartNative using FocusNode, Focus, FocusScope, and FocusScopeNode. Use when managing TextField focus programmatically (requestFocus/unfocus), handling onFocusChange callbacks, scoping focus traversal, or avoiding native first-responder sync races.
---

# DartNative Keyboard Focus Guide

DartNative manages keyboard focus by wiring `FocusNode` handles directly to platform first-responder states (`UIResponder.becomeFirstResponder` / `resignFirstResponder` on iOS, `View.requestFocus()` / `clearFocus()` on Android).

## Basic Focus Management (`FocusNode`)

Attach a `FocusNode` to a `TextField` and manage focus programmatically:

```dart
import 'package:dartnative/dartnative.dart';

class MyFormScreen extends StatefulWidget {
  const MyFormScreen({super.key});

  @override
  State<MyFormScreen> createState() => _MyFormScreenState();
}

class _MyFormScreenState extends State<MyFormScreen> {
  late final FocusNode _emailNode;
  late final FocusNode _passwordNode;

  @override
  void initState() {
    super.initState();
    _emailNode = FocusNode();
    _passwordNode = FocusNode();
  }

  @override
  void dispose() {
    // Release platform first-responder resources
    _emailNode.dispose();
    _passwordNode.dispose();
    super.dispose();
  }

  void _moveToPassword() {
    // Programmatically move focus to password input
    _passwordNode.requestFocus();
  }

  void _dismissKeyboard() {
    // Unfocus and dismiss soft keyboard
    _emailNode.unfocus();
    _passwordNode.unfocus();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(
          focusNode: _emailNode,
          hintText: 'Email',
          onSubmitted: (_) => _moveToPassword(),
        ),
        TextField(
          focusNode: _passwordNode,
          hintText: 'Password',
          obscureText: true,
        ),
        Button(
          title: 'Done',
          onPressed: _dismissKeyboard,
        ),
      ],
    );
  }
}
```

## Focus Traversal & State Callbacks (`Focus` & `FocusScope`)

Wrap custom input widgets in `Focus` to listen for focus state changes or manage a scope of focusable inputs:

```dart
// Track focus state for visual highlights
Focus(
  focusNode: _emailNode,
  onFocusChange: (focused) {
    print('Email field is focused: $focused');
  },
  child: const MyCustomInput(),
)

// FocusScope groups inputs
FocusScope(
  child: Column(
    children: [
      TextField(hintText: 'Field 1'),
      TextField(hintText: 'Field 2'),
    ],
  ),
)
```

## Gotchas

- **Post-Frame Request Focus:** In tap handlers or screen transition callbacks, defer `focusNode.requestFocus()` using `WidgetsBinding.instance.addPostFrameCallback` (or `Future.microtask`). Inline focus calls during tap events can race native first-responder synchronization and accidentally dismiss the keyboard instead of shifting focus.
- **Native Sync Handling:** `FocusNode.setHasFocusFromNative(bool)` updates internal `hasFocus` state when a user directly taps a native `TextField` (`editingDidBegin` / `editingDidEnd`), ensuring state stays in sync without double-triggering native FFI requests.
- **Node Disposal:** Always call `focusNode.dispose()` in your State's `dispose()` method to release platform FFI first-responder registrations.
