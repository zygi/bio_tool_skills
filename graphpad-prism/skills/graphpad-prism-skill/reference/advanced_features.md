# GraphPad Prism Scripting - Advanced Features Summary

## Graph Manipulation

### Changing Graphs
- Scripts can modify graph properties and formatting
- Allows automation of graph customization

### Exporting Graphs and Layouts
Commands for exporting visual output to various formats for use in publications and presentations.

## Data Export Commands

### ExportTable Command
**Syntax:** `ExportTable [filename] <ALL>`

**File Formats:**
- `.txt` - Tab-delimited text
- `.csv` - Comma-delimited text
- `.xml` - Prism XML format

**Behavior by Table Type:**
- **Data/Results tables:** Exports entire table
- **Info tables (`.txt`):** Exports notes only (not constants)
- **Info tables (`.xml`):** Exports both constants and notes
- **XML with ALL option:** Exports all data and info tables project-wide

**Note:** For info constants only, use WSheet command instead.

## Output to Tables and Files

### Writing to Prism Tables (Text/HTML/XML/SQL)
Scripts can create and populate Prism tables in multiple formats for structured data output.

### Writing to Text Files
Commands for writing data to external text files for export and integration.

### WBlock Command
**Introduced:** Prism 6.04/6.0e
**Purpose:** Write cell block (range) to text file opened via OpenOutput

**Syntax:** `WBlock [row(s)] [column(s)] <row label> <;>`

**Row Parameters:**
| Parameter | Meaning |
|-----------|---------|
| Single number | One row |
| `n-` | Row n through first blank row |
| `n-m` | Rows n through m (inclusive) |
| List | Multiple specific rows (any order) |

**Column Parameters:**
| Parameter | Meaning |
|-----------|---------|
| Single letter | One column |
| `A-` | Column A through first empty column |
| `A-D` | Columns A through D (inclusive) |
| List | Multiple specific columns (any order) |

**Row Label:** Optional label prepended to each row
**Semicolon (`;`):** If present, label joins with first column (no delimiter)

**Examples:**
```prism
WBlock "7-", "6,4", "PSummary"     // Rows 7-end, cols 6&4, labeled
WBlock "7-", "6,3", "Significant?";  // Label joins column (no delimiter)
WBlock "7-", "7,4", "PSummary"     // Label in separate column
```

**Output Format:** Tab-delimited by default (or comma if set in OpenOutput)

### WTBlock Command
**Syntax:** `WTBlock [row(s)] [columns to include] <row label> <;>`

**Difference from WBlock:** Transposes output - each Prism column becomes a row in output, with label applied per output row

**Example:**
```prism
WTBlock "7-", "7,3,4"  // Transpose specified rows/columns
```

## Microsoft Office Integration

### SendToWord Command
Sends current graph or layout to Microsoft Word document.

### SendToPP Command
Sends current graph or layout to Microsoft PowerPoint presentation.

**Platform:** Windows only

## Special Purpose Commands

### SetProjectName
**Syntax:** `SetProjectName ["new name"]`
Changes project file name for next save operation.

### SetScriptDialogTitle
**Syntax:** `SetScriptDialogTitle ["dialog title"]`
Customizes script progress dialog title (useful for user-facing scripts).

### RecalcAll
Recalculates all results in project. Required when opening files from prior Prism versions saved in large format.

### Beep
Plays audible alert when script completes (useful for long-running scripts).

### CreateLog
**Behavior:**
- **Script dialog execution:** Ignored (log always created in dialog tab 2)
- **File execution:** Creates `.log` file with same name as script file

Logs each script step for debugging and verification.

### SetSheetTitle
**Syntax:** `SetSheetTitle [new_name] <K>`
Renames current sheet.
**K parameter:** Retains first K characters of existing name, replaces remainder.

### CloseScriptDialog
Closes script progress dialog (useful for programmatic control).

### Print
Prints current sheet only (not entire project).

### Exit
**Syntax:** `Exit`
**Platform:** Windows only
Closes all open files without saving or prompting, then exits Prism.
**Warning:** Data loss risk - use cautiously.

## Scripting Syntax Elements

### Comments
```prism
// Single line comment

/*
  Multi-line
  comment block
*/
```

### Punctuation Rules
- **Command separation:** Space between command and arguments
- **Argument separation:** Spaces or commas
- **Case:** Insensitive (mix upper/lower freely)
- **Whitespace:** Extra spaces allowed for readability

### Documentation Conventions
- **Required arguments:** `[like this]` in square brackets
- **Optional arguments:** `<like this>` in angle brackets
- **Lowercase:** Placeholder for value you enter
- **UPPERCASE:** Exact keyword to type

### Output Variables
Available in commands that export text (especially useful in loops):

| Variable | Meaning |
|----------|---------|
| `%F` | Filename (before extension) |
| `%X` | File extension (without period) |
| `%S` | Current sheet name |
| `%N` | Loop iteration number |
| `%%` | Literal `%` symbol |

**Usage:** Can precede/follow with text
**Context:** Must be inside script loop (except `%S`)
**Additional:** More variables available for text writing commands

## Platform-Specific Features

### macOS Integration

#### Automator Support
**Requirements:** macOS 10.4 (Tiger) or later
**Input:** Script file path or script text
**Output:** "Done" or error message
Enables workflow automation with Prism scripts.

#### AppleScript/MacScript
Launch Prism from Microsoft Office (Word/Excel) via VBA:

```vba
MacScript "tell application ""HD:Prism4:Prism""" + Chr(13) + _
"activate" + Chr(13) + _
"open file ""Macintosh HD:Prism4:Scripts:dr2""" + Chr(13) + _
"end tell"
```

**Requirements:**
- Full path to Prism application
- Full path to script file

**Behavior:** VBA continues immediately (non-blocking)
**Synchronization:** VBA program must manually wait for Prism completion (e.g., poll for output file creation)

## Key Limitations and Notes

1. **Exit command:** Windows only, no macOS support
2. **SendToWord/SendToPP:** Windows-focused features
3. **WBlock/WTBlock:** Requires prior OpenOutput command
4. **Async execution:** AppleScript launches don't block - implement waiting logic
5. **Info table exports:** Different behavior for TXT vs XML formats
6. **File format dependencies:** Extension determines export format/behavior
