# Data Operations - Summary

## Creating New Sheets

### Commands
- `DuplicateDataTable <CLEAR>`
- `DuplicateFamily [prepend_text] <CLEAR>`
- `CreateLayout [#rows] [#columns] <orientation> <#StartingGraphIndex> <#Graphs>`

### Details
**DuplicateDataTable**: Duplicates only a data table (no linked analyses/graphs). Can import data but cannot analyze or graph within script.

**DuplicateFamily**: Duplicates sheet plus all linked sheets. Import new data replaces existing, all analyses/graphs update automatically. Prepended text added to all new sheet names. Use `%F` as prepend text to use imported filename.

**CreateLayout**: Creates new layout with array of placeholders.
- Orientation: "portrait" or "landscape" (default: portrait)
- Starting graph: rank order in navigator (1 = first graph)
- GraphsCount: number of graphs to assign (optional; stops at last placeholder or last graph if omitted)

## Modifying Data Tables

### Commands
- `SetYReplicates [#]`
- `ClearTable <row>,<col>,<subcol>`
- `DeleteAllDataSets <CLEAR>`

### Details
Scripts have limited ability to modify data tables. Cannot change table structure or number format (only Y replicates).

**SetYReplicates**: Changes number of Y replicates (subcolumns).

**DeleteAllDataSets**: Deletes all data and all links to analyses/graphs.

**ClearTable**: Erases all data left and below designated cell (including cell itself). Maintains links to analyses/graphs.

## Info Constants

### Commands
- `SetInfo ["value"] [rownumber] <position: 0 (name) or 1 (value) or 2 (notes)>`
- `SetValueToInfo [infotable #] [inforow#] [datarow] [datacol] <datasubcol>`

### Details
Both commands insert/change values in Info sheets.

**SetInfo**:
- Position 0: change constant name
- Position 1: change constant value (default)
- Position 2: append to notes (row number ignored)

Examples:
```
SetInfo 5.3, 3                        // Set 3rd constant = 5.3
Setinfo "Notebook 25, page 34", 5     // Set 5th constant to text
Setinfo "Experimenter", 3, 0          // Name 3rd constant
Setinfo "Dennis", 3, 1                // Set value of 3rd constant
Setinfo "Notebook 2007H, page 304", 1, 2  // Add to notes
```

**SetValueToInfo**: Value comes from data/results/info table. Must first navigate using Goto command. Specify source cell (row, col, subcol) and destination (info table #, info row #).

Example:
```
SetValueToInfo 2, 4, 14, 0  // From row 14, col 0 (X value) → 4th row of 2nd info table
```

## Importing Data

### From Text Files

#### Commands
- `Import <"filename">, <row>, <col>, <subcol>`
- `ImportLink <"filename">, <row>, <col>, <subcol>`

#### Details
**Import**: Imports data from text file into data table.
- If filename omitted in ForEach loop: uses current loop file
- If filename without path: uses SetPath directory
- Row 0 = title row, row 1 = first data row
- Column 0 = X column, column 1 = first Y column
- Column -1 = row titles
- Cannot import subcolumn titles

**ImportLink**: Replaces data in data object using existing filter/rearrangement choices. Before scripting, manually import file and create link with Filter/Placement tab options (select rows/columns, transpose, etc.). Properties stored with data object. ImportLink then updates using same properties.
- If single data object: omit row/col/subcol
- If multiple data objects: specify upper-left corner
- Only updates Prism→text file links (not Excel Paste Link)

### From Script File

#### Commands
- `InsertData <row>, <col>, <subcol>`
- `InsertDataLink <row>, <col>, <subcol>`

#### Details
For scripts created within other programs, data can be included in same file as script commands.

**InsertData**: Imports data included within script file, starting at specified position. Data enclosed in `<Data>` and `</Data>` tags. Script continues after `</Data>` tag.

Example:
```
InsertData
<Data>
1, 2, 4
5, 7, 9
</Data>
```

**InsertDataLink**: Imports data into data object at indicated position. Works similarly to ImportLink command.

Row/column indexing same as Import command (row 0 = title, col 0 = X, col -1 = row titles).

## Copy and Paste

### Commands
- `Copy`
- `Copy [#rows] [#columns] [first row] [first col] <first subcol>`
- `Paste [first row] [first col] <first subcol>`
- `Paste`

### Details
From data/info/results table, copy selected values to clipboard.

**Copy**:
- No arguments: copies entire sheet
- With arguments: specify range to copy
- #columns includes subcolumns (e.g., triplicate values in 2 columns = 6, or 7 with X)

**Paste**:
- No arguments: pastes entire table at current insertion point
- With arguments: specifies paste location

Copy/paste use system clipboard (overwrites other clipboard content).

Example:
```
Copy 1, 9, 5, 10  // Copies 1 row, 9 columns/subcolumns, starting row 5, col 10
```

## Opening and Saving Files

### Commands
- `Open ["filename"]`
- `Close`
- `Save <"filename">`

### Details
Scripts can be saved in Prism file or separate .pzc file. When script runs from separate file, Open/Close switch between Prism files. Commands work on last opened file. Multiple files can be open; reissue Open to switch back.

**Open**:
- Without disk/directory: uses SetPath directory (or temp folder if no SetPath)
- Include extension: .PZF, .PZFX, .PZT, or .PZM
- Unlike Import, doesn't auto-open next file in ForEach loop
- For ForEach loop: use `Open %F.PZFX` (%F = next file)

**Close**: Does not save file.

**Save**:
- Include filename: creates new file
- Omit filename: saves changes to same name
- Saves to .PZF or .PZFX based on extension

## Exporting Data

### Commands
- `ExportTable [filename] <ALL>`

### Details
Export format determined by file extension.

**From data/results table**: Exports entire table.
- Formats: .txt (tab-delimited), .csv (comma-delimited), .xml (Prism XML)

**From info table**:
- .txt file: exports only notes (not info constants)
- .xml file: exports both info constants and notes
- For info constants only: use WSheet command instead

**XML with ALL option**: Exports all data and info tables.

Can also export to script-created tables.
