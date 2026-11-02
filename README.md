# XLSX, JSON, and CSV conversions

Explicit conversions between SpreadsheetKit's XLSX sheets, JSONRows tables and
CSVKit records. `Import dataadapterskit` adds policy adapters; it does not add
another file parser, spreadsheet engine or JSON representation.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add dataadapterskit --git https://github.com/BuoyBay/dataadapterskit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.
BPM also resolves `spreadsheetkit`, `jsonrows`, `csvkit`.

## Example

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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer dataadapterskit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
