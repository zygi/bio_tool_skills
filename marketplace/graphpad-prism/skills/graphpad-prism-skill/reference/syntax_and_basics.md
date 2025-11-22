# GraphPad Prism Scripting - Syntax & Basics Summary

## Script Syntax

### Punctuation Rules
- Command name followed by space, then arguments
- Separate arguments with spaces or commas
- Case-insensitive
- Extra spaces allowed for readability

### Documentation Notation
- Required arguments: `[like this]`
- Optional arguments: `<like this>`
- Lowercase: value to enter
- UPPERCASE: exact word to type

## Comments

### Syntax
```
// single line comment

/*
 multiple lines
 of comments
*/
```

## Row and Column Numbering

### Columns
- Row Title column: -1
- X Column: 0
- Column A: 1
- Column B: 2, etc.

### Rows
- Column Title row: 0 or -1
- First data row: 1 (not 0)
- Subcolumn titles: not accessible via script

## General Commands

### GoTo Command
Navigate between sheets and sections.

**Syntax:**
- `GoTo <section: D, I, R, G, or L>, <sheet#>, V<tab#>`
- `GoTo +` (next sheet)
- `GoTo -` (previous sheet)

**Examples:**
- `GoTo G` - go to graphs section, most recent graph
- `GoTo 3` - third sheet of current section
- `GoTo G, 3` - third graph
- `GoTo V2` - second analysis tab of current results
- `GoTo R, 3, V2` - second tab of third results sheet

**Notes:**
- Sheet numbering starts at 1 (not 0)
- Space required between results and view: `R4V2` fails, use `R, 4, V2`

## Loops and Simulations

### Loop Commands
- `ForEach [# of loops]` - loop specified number of times
- `ForEach [filespec]` - loop over files (e.g., `ForEach *.CSV` or `ForEach "C:\data\DR*.txt"`)
- `ForEachSheet` - loop over all sheets in current section
- `Next` - end loop statement
- `Regenerate` - regenerate random numbers (for Monte Carlo simulations)

**Notes:**
- Cannot nest loops
- Used for simulations and repeated file imports

### Monte Carlo Pattern
1. Create Prism file with "Simulate data with random scatter" analysis
2. Link simulated data to another analysis (e.g., nonlinear regression)
3. In loop: GoTo simulation analysis → Regenerate → GoTo results → write output → Next

## Variables for Text Output

Available in any command that exports text, primarily useful in loops:

- `%F` - file name (before period)
- `%X` - file extension (without period)
- `%S` - current sheet name (works inside or outside loops)
- `%N` - loop number
- `%%` - literal percent symbol (%)

Variables can be preceded/followed by other text. Additional variables available when writing text files.

## File Path Management

### SetPath Command
**Syntax:** `SetPath ["drive and folder path"]`

Sets default path for file read/write commands unless overridden in individual commands.

**Windows Examples:**
```
SetPath "C:\Users\(current user)\Documents\007\goldfinger\oddjob"
SetPath "(local drive):\DataFiles\August2005\"
SetPath "\\LabServer\DDrive\PrismFiles"
```

**Mac Example:**
```
SetPath "Hard Drive:Prism 4:Data:June21"
```

**Network drives:** Use `\\ServerName\DriveName\path` format without "C:" notation.

Individual commands can specify full paths to override SetPath.
