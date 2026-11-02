---
title: "dataadapterskit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["CSVConversion", "CSVWriteResult", "TableConversion"]
dependencies: ["csvkit", "jsonrows", "spreadsheetkit"]
symbols: ["CSVConversion", "CSVWriteResult", "TableConversion"]
related: ["api.md", "getting-started.md", "index.md"]
---

# dataadapterskit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## TableConversion

`Public Class TableConversion`

Immutable table conversion metadata. Table itself is immutable JSONRows data.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Table` | `JSONRowsTable` | Yes | No | — | — |
| `Columns` | `Array Of String` | Yes | No | — | — |
| `MissingValues` | `Integer` | Yes | No | — | — |
| `NullValues` | `Integer` | Yes | No | — | — |
| `FormulaValues` | `Integer` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(table As JSONRowsTable, columns As Array Of String, missing As Integer, nulls As Integer, formulas As Integer)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `table` | `JSONRowsTable` | `ByVal` | Required |
| `columns` | `Array Of String` | `ByVal` | Required |
| `missing` | `Integer` | `ByVal` | Required |
| `nulls` | `Integer` | `ByVal` | Required |
| `formulas` | `Integer` | `ByVal` | Required |

Returns: no value.

## CSVConversion

`Public Class CSVConversion`

CSV text and counts of values whose distinct semantics were flattened.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Text` | `String` | Yes | No | — | — |
| `MissingValues` | `Integer` | Yes | No | — | — |
| `NullValues` | `Integer` | Yes | No | — | — |
| `NestedValues` | `Integer` | Yes | No | — | — |
| `FormulaValues` | `Integer` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(text As String, missing As Integer, nulls As Integer, nested As Integer, formulas As Integer = 0)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `text` | `String` | `ByVal` | Required |
| `missing` | `Integer` | `ByVal` | Required |
| `nulls` | `Integer` | `ByVal` | Required |
| `nested` | `Integer` | `ByVal` | Required |
| `formulas` | `Integer` | `ByVal` | `0` |

Returns: no value.

### JSONRowsCSV

`Public Function JSONRowsCSV(table As JSONRowsTable, missingValue As String = "", nullValue As String = "", nestedJSON As Boolean = False, maxCharacters As Integer = 1000000) As CSVConversion`

Export first-seen union columns, with a header and canonical CSV quoting.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `table` | `JSONRowsTable` | `ByVal` | Required |
| `missingValue` | `String` | `ByVal` | `""` |
| `nullValue` | `String` | `ByVal` | `""` |
| `nestedJSON` | `Boolean` | `ByVal` | `False` |
| `maxCharacters` | `Integer` | `ByVal` | `1000000` |

#### Returns

| Type |
| --- |
| `CSVConversion` |

### JSONRowsCSVColumns

`Public Function JSONRowsCSVColumns(table As JSONRowsTable, columns As Array Of String, missingValue As String = "", nullValue As String = "", nestedJSON As Boolean = False, maxCharacters As Integer = 1000000) As CSVConversion`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `table` | `JSONRowsTable` | `ByVal` | Required |
| `columns` | `Array Of String` | `ByVal` | Required |
| `missingValue` | `String` | `ByVal` | `""` |
| `nullValue` | `String` | `ByVal` | `""` |
| `nestedJSON` | `Boolean` | `ByVal` | `False` |
| `maxCharacters` | `Integer` | `ByVal` | `1000000` |

#### Returns

| Type |
| --- |
| `CSVConversion` |

### XLSXSheetRows

`Public Function XLSXSheetRows(sheet As XLSXSheet, headerRow As Integer = 1, maxRows As Integer = 10000, maxColumns As Integer = 1000, maxCharacters As Integer = 1000000, allowCachedFormulas As Boolean = False) As TableConversion`

A contiguous, exact unique String header maps sparse cells to JSON fields.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `sheet` | `XLSXSheet` | `ByVal` | Required |
| `headerRow` | `Integer` | `ByVal` | `1` |
| `maxRows` | `Integer` | `ByVal` | `10000` |
| `maxColumns` | `Integer` | `ByVal` | `1000` |
| `maxCharacters` | `Integer` | `ByVal` | `1000000` |
| `allowCachedFormulas` | `Boolean` | `ByVal` | `False` |

#### Returns

| Type |
| --- |
| `TableConversion` |

### XLSXSheetCSV

`Public Function XLSXSheetCSV(sheet As XLSXSheet, headerRow As Integer = 1, missingValue As String = "", nullValue As String = "", maxRows As Integer = 10000, maxColumns As Integer = 1000, maxCharacters As Integer = 1000000, allowCachedFormulas As Boolean = False) As CSVConversion`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `sheet` | `XLSXSheet` | `ByVal` | Required |
| `headerRow` | `Integer` | `ByVal` | `1` |
| `missingValue` | `String` | `ByVal` | `""` |
| `nullValue` | `String` | `ByVal` | `""` |
| `maxRows` | `Integer` | `ByVal` | `10000` |
| `maxColumns` | `Integer` | `ByVal` | `1000` |
| `maxCharacters` | `Integer` | `ByVal` | `1000000` |
| `allowCachedFormulas` | `Boolean` | `ByVal` | `False` |

#### Returns

| Type |
| --- |
| `CSVConversion` |

### JSONRowsCSVToSink

`Public Function JSONRowsCSVToSink(table As JSONRowsTable, sink As CSVByteSink, missingValue As String = "", nullValue As String = "", nestedJSON As Boolean = False, maxBytes As Integer = 1000000, maxRecordBytes As Integer = 1000000) As CSVWriteResult`

Export the immutable table's encounter-order columns to a synchronous sink.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `table` | `JSONRowsTable` | `ByVal` | Required |
| `sink` | `CSVByteSink` | `ByVal` | Required |
| `missingValue` | `String` | `ByVal` | `""` |
| `nullValue` | `String` | `ByVal` | `""` |
| `nestedJSON` | `Boolean` | `ByVal` | `False` |
| `maxBytes` | `Integer` | `ByVal` | `1000000` |
| `maxRecordBytes` | `Integer` | `ByVal` | `1000000` |

#### Returns

| Type |
| --- |
| `CSVWriteResult` |

### JSONRowsCSVColumnsToSink

`Public Function JSONRowsCSVColumnsToSink(table As JSONRowsTable, columns As Array Of String, sink As CSVByteSink, missingValue As String = "", nullValue As String = "", nestedJSON As Boolean = False, maxBytes As Integer = 1000000, maxRecordBytes As Integer = 1000000) As CSVWriteResult`

Clone projection columns, then emit a header and one UTF-8 record at a time.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `table` | `JSONRowsTable` | `ByVal` | Required |
| `columns` | `Array Of String` | `ByVal` | Required |
| `sink` | `CSVByteSink` | `ByVal` | Required |
| `missingValue` | `String` | `ByVal` | `""` |
| `nullValue` | `String` | `ByVal` | `""` |
| `nestedJSON` | `Boolean` | `ByVal` | `False` |
| `maxBytes` | `Integer` | `ByVal` | `1000000` |
| `maxRecordBytes` | `Integer` | `ByVal` | `1000000` |

#### Returns

| Type |
| --- |
| `CSVWriteResult` |

## CSVWriteResult

`Public Class CSVWriteResult`

Successful sink export counts. No CSV text is retained.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `BytesWritten` | `Integer` | Yes | No | — | — |
| `RecordsWritten` | `Integer` | Yes | No | — | — |
| `MissingValues` | `Integer` | Yes | No | — | — |
| `NullValues` | `Integer` | Yes | No | — | — |
| `NestedValues` | `Integer` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(bytesWritten As Integer, recordsWritten As Integer, missingValues As Integer, nullValues As Integer, nestedValues As Integer)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `bytesWritten` | `Integer` | `ByVal` | Required |
| `recordsWritten` | `Integer` | `ByVal` | Required |
| `missingValues` | `Integer` | `ByVal` | Required |
| `nullValues` | `Integer` | `ByVal` | Required |
| `nestedValues` | `Integer` | `ByVal` | Required |

Returns: no value.
