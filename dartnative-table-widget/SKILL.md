---
name: dartnative-table-widget
description: Guide for building grid and tabular layouts in DartNative using the pure-Dart Table, TableRow, TableBorder, and column width policies.
---

# DartNative Table Widget

`Table` is a pure-Dart tabular layout widget composed of `Column`, `Row`, `Expanded`, and `Container` primitives. It requires no custom native platform views.

---

## Key Characteristics & API

```dart
Table({
  Map<int, TableColumnWidth>? columnWidths,
  TableColumnWidth defaultColumnWidth = const FlexColumnWidth(),
  TableBorder? border,
  required List<TableRow> children,
})
```

---

## Column Width Policies (`TableColumnWidth`)

- **`FlexColumnWidth([double value = 1.0])`**: Sizes column proportionally to other flex columns.
- **`FixedColumnWidth(double width)`**: Sizes column to an absolute point width.
- **`IntrinsicColumnWidth()`**: API compatibility alias (behaves like `FlexColumnWidth(1.0)`).

---

## Table Borders (`TableBorder`)

Inner grid lines and outer borders are drawn automatically around rows and cells:

```dart
Table(
  border: TableBorder.all(
    color: Colors.grey.shade300,
    width: 1.0,
  ),
  columnWidths: const {
    0: FixedColumnWidth(80),
    1: FlexColumnWidth(2),
    2: FlexColumnWidth(1),
  },
  children: const [
    TableRow(
      decoration: BoxDecoration(color: Color(0xFFF5F5F5)),
      children: [
        Text('ID', style: TextStyle(fontWeight: FontWeight.bold)),
        Text('Item', style: TextStyle(fontWeight: FontWeight.bold)),
        Text('Price', style: TextStyle(fontWeight: FontWeight.bold)),
      ],
    ),
    TableRow(
      children: [
        Text('1'),
        Text('Widget A'),
        Text('\$10.00'),
      ],
    ),
  ],
)
```

---

## Performance Advantage

Because `Table` composes pure Dart widgets (`Column`, `Row`, `Container`), it runs efficiently across all supported platforms without needing separate native view bindings.

---

## Platform Compatibility Matrix

| Component | Lowering | Platform Dependency |
|---|---|---|
| `Table` | Composed `Column` / `Row` / `Container` | Cross-platform pure Dart |
