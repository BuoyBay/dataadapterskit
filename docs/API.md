# DataAdaptersKit 0.1.0

Explicit conversions between SpreadsheetKit's XLSX sheets, JSONRows tables and
CSVKit records. `Import dataadapterskit` adds policy adapters; it does not add
another file parser, spreadsheet engine or JSON representation.

## API

- `XLSXSheetRows(sheet, headerRow = 1, maxRows = 10000, maxColumns = 1000,
  maxCharacters = 1000000, allowCachedFormulas = False) As TableConversion`.
- `JSONRowsCSV(table, missingValue = "", nullValue = "", nestedJSON = False,
  maxCharacters = 1000000) As CSVConversion`.
- `JSONRowsCSVColumns(table, columns, missingValue = "", nullValue = "",
  nestedJSON = False, maxCharacters = 1000000) As CSVConversion`.
- `XLSXSheetCSV(sheet, headerRow = 1, missingValue = "", nullValue = "",
  maxRows = 10000, maxColumns = 1000, maxCharacters = 1000000,
  allowCachedFormulas = False) As CSVConversion`.

`TableConversion.Table` is the immutable JSONRows table; `Columns` is a defensive
copy of the authoritative XLSX header order. `MissingValues`, `NullValues` and
`FormulaValues` describe the converted sheet data. JSONRows itself derives its
columns from present fields: all-missing or header-only columns therefore appear
in `TableConversion.Columns` but may not appear in `Table.Columns()`. Use the
explicit schema when exporting such a table; `XLSXSheetCSV` does this for you.

`CSVConversion.Text` is canonical CSV with a header and CRLF endings.
`MissingValues`, `NullValues`, `NestedValues` and `FormulaValues` report flattening
or cached-formula use. Counts apply to selected cells, not unique columns.
Constructors are public for report composition and validate nonnegative counts;
caller-created reports do not prove that an actual conversion occurred.

## XLSX policy

The designated header row must contain contiguous, nonempty literal String cells
starting at column 1, with exact case-sensitive unique names. Headers are not
trimmed, renamed or deduplicated. Formula headers reject. Rows before the header
are ignored. Stored data cells past the header's width reject instead of being
silently discarded. Data rows extend through the last stored row, preserving
intermediate entirely missing rows as empty JSON objects.

- Missing coordinates become absent object fields.
- Explicit blank cells become JSON null.
- String and ISO `date-text` values remain exact strings.
- Numbers become finite Double values through SpreadsheetKit.NumberValue; original
  decimal spelling/precision may be lost. Excel date serials remain numbers.
- Boolean cells become JSON booleans. Error cells reject.
- Formulas reject by default. Explicit `allowCachedFormulas=True` converts their
  stored cache using the same rules and counts them; no-cache formulas become
  null. The cache may be stale. Nothing evaluates or updates a formula.

Header-only sheets export their header. No-header sheets reject. Duplicate or
invalid coordinates in caller-created sheets reject too. The input is unchanged.

## CSV policy

`JSONRowsCSV` uses JSONRows' first-seen column union. `JSONRowsCSVColumns` accepts
an explicit ordered projection, rejects duplicate names, and allows absent names
so an external schema can supply all-missing columns. Unselected fields are
intentionally omitted. Empty names in JSON projections are valid.

Strings retain exact text. Boolean/numeric JSON scalars use compact JSON spelling,
which is locale-independent and follows Buoy's Double JSON representation.
Missing and null use their separate caller-selected replacements (both default
to empty text); metadata keeps their counts. CSV cannot retain their original
type distinction. Nested arrays/objects reject unless `nestedJSON=True`, in which
case each becomes one compact JSON field. CSVKit handles quoting, commas, newlines
and singleton empty fields. No spreadsheet formula escaping is added.

An empty zero-column table produces empty text. Nonempty zero-column rows reject
because CSV cannot represent their shape unambiguously. An explicit nonempty
projection emits its header even for an empty table.

## Bounds and allocation

At most 100,000 data rows and 1,000 columns are accepted, and the selected/densified
grid must contain at most one million positions. XLSX source cells are capped at
one million. `maxCharacters` is 1..16,000,000 Unicode codepoints and bounds the
serialized JSON intermediate and CSV output independently. CSV conversion checks
rows against the remaining output budget before retaining them. JSON conversion
checks each serialized row before adding it to the output. These are semantic
bounds, not a strict process-memory budget: each row/escaped field and the final
serialization may allocate before a length check, and the input tables/sheets
already exist in memory. Imported XLSX ZIP/XML allocations retain SpreadsheetKit's
own documented limits. This is whole-table conversion, not streaming.

## Dependencies and package archive

The BPM manifest declares dependencies on `spreadsheetkit`,
`jsonrows` and `csvkit`. Sealed consumers need those archives plus Buoy's bundled
ZIP/XML archives. `Import spreadsheetkit` explicitly when calling `ReadXLSX` in consumer code.

```sh
mkdir -p dist
"${BPM_PACKAGER:-buoy}" pkg seal . -o dist/dataadapterskit-0.1.0.buipkg
```

## Export a JSONRows table to a byte sink

`JSONRowsCSVToSink(table, sink, missingValue = "", nullValue = "",
nestedJSON = False, maxBytes = 1000000, maxRecordBytes = 1000000)` writes
encounter-order columns. `JSONRowsCSVColumnsToSink(table, columns, sink, ... )`
uses the supplied projection order. Both return a `CSVWriteResult` with read-only
`BytesWritten`, `RecordsWritten`, `MissingValues`, `NullValues` and `NestedValues`.
The result contains no CSV text. Its public constructor rejects negative counts.

The existing CSV convenience functions retain their character-based output limits
and return complete text. Sink exports use UTF-8 **byte** limits, including
quoting, delimiters and CRLF. `maxBytes` and `maxRecordBytes` must each be
1..16000000. At most 100000 source rows, 1000 projected columns and one million
projected positions are accepted. `RecordsWritten` includes the header. An empty
projection on an empty table emits nothing; nonempty rows with no columns reject.
A header-only projection emits one record even when the table has no rows.

The sink is CSVKit's synchronous `CSVByteSink`. Conversion shares the convenience
API's exact cell policy: missing/null replacements, JSON scalar formatting,
explicit permission for nested JSON and associated counts. Duplicate projection
columns reject before output. Columns are copied before the first callback, so
callbacks may change the caller's array without changing this export. JSONRowsTable
is already immutable: its exposed arrays and JSON cells are copies. The adapter
retains the original materialized table; it does **not** stream source parsing.

Only the header and current formatted row are retained, rather than a collection
of all CSV output. The underlying `CSVStreamWriter` enforces each row's byte cap
before calling the sink. Scalar-bounded formatting, nested-value serialization and
UTF-8 encoding still use per-row intermediates; an encoding buffer may reach four
times the scalar formatting cap before its byte check. Existing source table
storage, caller allocations and chunks retained by the sink remain outside this
output-retention guarantee. Nested JSON is serialized before formatting, so its
allocation is bounded by the source table's input limits, not `maxRecordBytes`.

Any conversion, limit or sink exception escapes and no result is returned. Earlier
records may already be written. A sink can also write a partial record before
throwing; there is no rollback or implicit retry. In particular, refusing a nested
value may occur after the header and earlier rows. The adapter does not close or
flush the sink. Each invocation has its own writer; recursive exports to separate
sinks are independent. Sharing a sink between recursive or concurrent exports may
interleave records: callers must serialize access. No global lock is imposed.

For a file export, supply a sink while keeping its stream lifecycle explicit:

```basic
Import dataadapterskit

Class TableFileSink Implements CSVByteSink
    Public Property Stream As BinaryStream
    Public Sub WriteCSVBytes(data As Bytes)
        Stream.Write(data)
    End Sub
End Class

Sub Main(args As Array Of String)
    Dim table As New JSONRowsTable(New FileSystemItem(args(1)).ReadAllText().Value)
    Dim opened As Result Of BinaryStream, IOError = BinaryStream.Create(New FileSystemItem(args(2)), True)
    If opened.IsErr Then Throw New Exception("Cannot create output")
    Dim stream As BinaryStream = opened.Value
    Dim sink As New TableFileSink()
    sink.Stream = stream
    Try
        Dim counts As CSVWriteResult = JSONRowsCSVToSink(table, sink, "M", "NULL", True)
        Print Str(counts.RecordsWritten)
    Finally
        stream.Close()
    End Try
End Sub
```

The destination can contain partial output on failure. Choose a temporary file and publish it separately if the application needs atomic replacement. Installed ZIP/XML dependency archives are required.
