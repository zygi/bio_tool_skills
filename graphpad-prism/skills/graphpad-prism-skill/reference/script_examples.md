# GraphPad Prism Script Examples

## Overview

Seven examples demonstrating scripting use cases: repeated imports, Monte Carlo analysis, multi-file consolidation, template workflows, embedded data, multi-table operations, and batch graph formatting.

## Example 1: Repeatedly Import Data

**Use case**: Import multiple data files sequentially into separate tables.

**Key commands**:
- `GoTo D, N` - Navigate to data table N
- `Import filename.txt` - Import external file
- Manual repetition of commands for each file/table

**Workflow**:
1. Navigate to target table
2. Import file
3. Repeat for each additional file

**Limitation**: No loop construct in this basic example; requires explicit command repetition.

## Example 2: Monte Carlo Analysis

**Use case**: Perform iterative simulations with randomization.

**Key commands**:
- `ForEach N` - Loop N times
- `GoTo R, N` - Navigate to results table N
- `AnalyzeData` - Execute analysis
- `Next` - End loop iteration

**Workflow**:
1. Set up analysis on initial data
2. Loop through iterations:
   - Generate random data variation
   - Execute analysis
   - Store results in indexed table
3. Aggregate results across iterations

**Pattern**:
```
ForEach 100
  GoTo R, %N
  AnalyzeData
  Next
```

**Variable**: `%N` holds current iteration number (1-based).

## Example 3: Import Several Files onto One Table

**Use case**: Consolidate multiple data files into columns of single table.

**Key commands**:
- `GoTo D, 1` - Navigate to data table 1
- `Import filename.txt, column=N` - Import into specific column N
- Column parameter specifies destination

**Workflow**:
1. Navigate to target table
2. Import first file to column 1
3. Import subsequent files to columns 2, 3, etc.

**Syntax**: `Import Data1.txt, column=1`

**Note**: Files must have compatible row structure.

## Example 4: Open Template and Import Data

**Use case**: Automate data import into pre-configured analysis template.

**Key commands**:
- `Open template.pzfx` - Load template file
- `GoTo D, 1` - Navigate to data table
- `Import datafile.txt` - Import data
- `SaveAs output.pzfx` - Save populated file

**Workflow**:
1. Open template with pre-configured graphs/analyses
2. Navigate to data table
3. Import new data
4. Save as new file (preserves template)

**Advantage**: Template contains all formatting, analysis settings, and graph configurations.

## Example 5: Import Data and Info Constants from Script File

**Use case**: Embed data and metadata directly in script (no external files).

**Key commands**:
- `<DATA>...</DATA>` - Data block delimiter
- `InsertData N, M` - Insert data block N starting at column M
- `SetInfo constant_name, value` - Set info constant

**Data block syntax**:
```
<DATA>
value1\tvalue2\tvalue3
value4\tvalue5\tvalue6
</DATA>
```

**Info constant syntax**:
```
SetInfo Experiment, "Drug Study A"
SetInfo Date, "2024-01-15"
SetInfo Concentration, 10.5
```

**Workflow**:
1. Define data blocks in script using `<DATA>` tags
2. Navigate to target table: `GoTo D, 1`
3. Insert data: `InsertData 1, 0` (block 1, starting column 0)
4. Navigate to info table: `GoTo I, 2`
5. Set info constants with name/value pairs

**Data format**: Tab-delimited values within DATA tags, numbered sequentially (1-based).

**Info types**: String (quoted), numeric (unquoted), date (quoted).

## Example 6: Import Data into Several Tables

**Use case**: Batch import multiple files into multiple tables using loop.

**Prerequisites**: Prism file with N data tables, each linked to graph, all graphs on one layout.

**Key commands**:
- `ForEach N` - Loop N times
- `GoTo %N` - Navigate to table using iteration variable
- `Import Data%N.txt` - Dynamic filename with variable substitution
- `Next` - End loop
- `GoTo L, 1` - Navigate to layout
- `Print` - Print layout

**Script pattern**:
```
GoTo D
ForEach 9
  GoTo %N
  Import Data%N.txt
  Next
GoTo L, 1
Print
```

**Variable substitution**: `%N` replaced with current iteration number (1, 2, 3, ..., 9).

**Dynamic filenames**: `Data1.txt`, `Data2.txt`, ..., `Data9.txt` matched to tables 1-9.

**Result**: All tables populated, layout printed with all graphs updated.

## Example 7: Changing X-Axis Title of All Graphs

**Use case**: Apply uniform formatting change across all graphs in file.

**Key commands**:
- `GoTo G` - Navigate to graphs family
- `ForEachSheet` - Iterate over all sheets in current family
- `SetAxisTitle axis, "title"` - Set axis title
- `Next` - End loop

**Script pattern**:
```
GoTo G
ForEachSheet
  SetAxisTitle X, "Time (minutes)"
  Next
```

**Axis parameter**: `X` (X-axis), `Y` (Y-axis), `Y2` (secondary Y-axis).

**Scope**: Operates on all graph sheets in current family.

**Alternative axes**: `SetAxisTitle Y, "Response (units)"` for Y-axis.

## Common Scripting Patterns

### Loop Constructs
- `ForEach N` / `Next` - Fixed iteration count
- `ForEachSheet` / `Next` - Iterate over sheets in family
- `%N` variable - Current iteration number (1-based)

### Navigation
- `GoTo family, N` - Navigate to specific sheet
  - `D, N` - Data table N
  - `R, N` - Results table N
  - `G, N` - Graph N
  - `I, N` - Info table N
  - `L, N` - Layout N
- `GoTo family` - Navigate to family without specific sheet
- `GoTo %N` - Navigate using iteration variable

### Data Operations
- `Import filename.txt` - Import external file
- `Import filename.txt, column=N` - Import to specific column
- `InsertData N, M` - Insert embedded data block N at column M
- `<DATA>...</DATA>` - Embed data in script (tab-delimited)

### Info Constants
- `SetInfo name, value` - Set named constant
- String values: quoted
- Numeric values: unquoted

### File Operations
- `Open filename.pzfx` - Load Prism file
- `SaveAs filename.pzfx` - Save with new name

### Analysis and Output
- `AnalyzeData` - Execute analysis
- `Print` - Print current sheet/layout

## Variable Substitution

`%N` - Current iteration number in loops
- Use in navigation: `GoTo %N`
- Use in filenames: `Import Data%N.txt`
- Use in any string context requiring iteration number
- 1-based indexing (first iteration = 1)

## Data Formats

**External files**: Tab-delimited or comma-delimited text files.

**Embedded data**: Tab-delimited within `<DATA>` tags, multiple blocks numbered sequentially.

**Info constants**: Name/value pairs, supports strings (quoted), numbers (unquoted), dates (quoted).

## Script Structure Best Practices

1. Navigate before operating: Always `GoTo` target before import/analysis
2. Use loops for repetitive operations: `ForEach` with `%N` variable
3. Embed small datasets: Use `<DATA>` blocks for portability
4. Template workflows: Separate data from analysis configuration
5. Batch operations: Combine loops with variable substitution for file series
6. Layout printing: Navigate to layout after updating all graphs

## Limitations and Notes

- No automatic file discovery: Must specify exact filenames or use predictable naming pattern
- Fixed iteration counts: `ForEach N` requires known count, no dynamic termination
- Sequential execution: No parallel processing
- Column compatibility: Multi-file imports require matching row structures
- Variable scope: `%N` only valid within loop construct
- 1-based indexing: All sheet numbers, iteration counts, and data blocks start at 1
