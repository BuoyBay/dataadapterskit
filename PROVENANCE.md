# Provenance and scope

Independent Buoy adapter code written to compose the repository's SpreadsheetKit,
JSONRows and CSVKit APIs. No upstream Xojo source, workbook, artwork or fixture was
copied. The source collections motivated the underlying packages; this package's
conversion policies are new and intentionally explicit rather than a compatibility
claim about those projects. See the dependencies' own provenance documents.

Tests construct minimal OOXML ZIP parts using Python's standard `zipfile`; the
JSON/CSV oracle uses Python's standard `json` and `csv` modules. Synthetic fixtures
contain no external relationships, macros or remote resources. The adapter calls
no network, shell extraction, formula engine or external spreadsheet program.
