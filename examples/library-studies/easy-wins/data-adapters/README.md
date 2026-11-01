# Data adapters example

This console example prints a typed JSON table as CSV. Given an XLSX path, it
converts the first sheet using row 1 as a unique header and explicitly permits
cached formula values. It prints missing/null counts without evaluating formulas
or modifying the workbook.

From this directory, run `bpm install`. Place the ZIP and XML archives supplied
by your installed Buoy toolchain in `packages/`, then build:

```sh
mkdir -p dist
bpm build main.bui -o dist/example
./dist/example
./dist/example workbook.xlsx
```

Read the [package policies](../../../../docs/API.md) before using conversion
output. CSV flattens types, replacements for missing/null values are explicit,
and cached formulas can be stale. The adapter retains an external header schema
when JSONRows' observed column union would omit an all-missing column.
