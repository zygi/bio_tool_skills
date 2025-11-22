# GraphPad Prism Scripts - Overview & Running Scripts

## What are Prism Scripts?

**Purpose**: Automate analysis of multiple files or perform Monte Carlo simulations. Scripts (.pzc files) can open/close Prism files, import data, export/print results and graphs.

**Key Limitation**: Scripts do NOT control how Prism analyzes or graphs data - that's defined in the Prism project file. Scripts only import data, specify which results to export/print, and manage file operations.

**Automation Built-in**: Prism links data→graphs→analyses. Editing/replacing data or info constants auto-updates everything (analyses, graphs, paste-linked data). Scripts extend this for batch processing.

**Skill Level**: Creating scripts slightly harder than using Prism. Scripting/macro language experience helpful but not required.

## Alternatives to Scripting

**PZFX File Format**: Other programs can manipulate Prism files without scripts:
1. Prism saves project files in PZFX format (plain text XML)
2. Analysis/graphing sections encrypted, but data/info tables are plain XML
3. External programs can:
   - Open PZFX file with text editor
   - Edit/replace data and info constants in XML
   - Resave file
   - When user opens in Prism, graphs/analyses auto-update

**Workflow**:
1. Create Prism file with desired analysis/graphs
2. Save as PZFX
3. Open in text editor - see encrypted analysis + plain XML data
4. Edit XML data
5. Resave
6. Open in Prism - updates automatically

**Advantages**: Cross-platform, no need for simultaneous execution, simpler for some use cases.

## Running Scripts from Prism (Development/Testing)

**Access**: Prism button → File menu → Run Script

### Creating New Script:
1. Choose "Run Script"
2. Select "New Script"
3. Choose blank screen or copy existing script
4. Edit script in left pane, view syntax reference in right pane
5. Test with "1 step" button (step through line-by-line)
6. Use "12 steps" for batch testing
7. Click "Run" when ready - Prism and dialog minimize during execution

### Editing Existing Script:
1. Choose "Run Script"
2. Select from script list OR "Open Script file" for external file
3. Follow steps 4-7 above

## Running Scripts on Mac (Automator, MacScript, AppleScript)

### Automator (OSX 10.4+)
- Prism installs Automator Action for script launching
- **Input**: File containing script OR script text
- **Output**: "Done" or error message

### MacScript (from Microsoft Office VBA)
**Launch syntax**:
```vb
MacScript "tell application ""HD:Prism4:Prism""" + Chr(13) + _
"activate" + Chr(13) + _
"open file ""Macintosh HD:Prism4:Scripts:dr2""" + Chr(13) + _
"end tell"
```

**Synchronization Pattern** (VBA doesn't wait for Prism):
1. Prism script creates completion file:
   ```
   OpenOutput "done.txt"
   WText "done"
   CloseOutput
   ```
2. VBA deletes done.txt before launching Prism
3. VBA loops until done.txt exists:
   ```vb
   Do Until Dir$("HD:prism 5:done.txt") > ""
   Application.Wait Now + TimeValue("00:00:1")
   Loop
   ```
4. VBA quits Prism:
   ```vb
   MacScript "tell application ""HD:Prism4:Prism""" + Chr(13) + _
   "quit" + Chr(13) + _
   "end tell"
   ```

### AppleScript

**HFS Path Example**:
```applescript
tell application "HD:Applications:Prism4 Folder:Prism"
   activate
   open file "HD:P4scripts:DR2.pzc"
end tell
```

**POSIX Path Example**:
```applescript
tell application "HD:Applications:Prism4 Folder:Prism"
   activate
   open POSIX file "/P4scripts/DR2.pzc"
end tell
```

**Note**: POSIX paths use slashes (not colons), must start with `/`.

## Running Scripts on Windows

### Desktop Shortcut
**Syntax**:
```
"C:\PRISM5\PRISM.EXE" @"C:\PROGRAM FILES\PRISM 6\SCRIPTS\DOIT.PZC"
```
- Use `@` symbol before script path
- Quote paths containing spaces

### Shell Command (from VBA/other programs)
**Basic syntax**:
```vb
Shell ("C:\prism 5\prism.exe @C:\prism 6\dr2.pzc")
```

**Behavior**:
- Prism runs invisibly
- Shell returns immediately (doesn't wait for completion)

**Synchronization Pattern** (same as Mac):
1. Prism script creates done.txt:
   ```
   OpenOutput "done.txt"
   WText "done"
   CloseOutput
   ```
2. VBA deletes done.txt, launches Prism, then loops:
   ```vb
   Do Until Dir$("C:\prism 6\done.txt") > ""
   Application.Wait Now + TimeValue("00:00:1")
   Loop
   ```

### OLE/ActiveX Automation
**Full example**:
```vb
Set Prism = CreateObject("Prism.command")
Prism.SetPath ("C:\data\july99")
Prism.visible
Prism.runcommand ("c:\prism5\doseresp.pzc")
Prism.quit
Set Prism = Nothing
```

**Commands**:
- `CreateObject("Prism.command")` - Creates Prism object (Windows finds prism.exe via registry)
- `SetPath (path)` - Optional, sets working directory for data files/templates
- `visible` - Shows progress dialog (remove for invisible operation)
- `runcommand (scriptpath)` - Launches script (requires full path)
- `quit` - Exits Prism

**Advantages over Shell**:
1. Sequential execution - VBA waits for Prism completion (no synchronization file needed)
2. Can set working directory without modifying script

### Intranet Launch (Batch File)
**Create StartPrism.bat**:
```batch
Call "N:\Program Files\PRISM5\PRISM.EXE" -OC:\WINDOWS\prism5.cnf
```
OR:
```batch
Call F:\Prism\Prism.exe @"F:\PRISM\PRISMSCRIPTS\DOIT.PZC"
```

**HTML link**: Link text to StartPrism.bat file.

## Excel Integration

### Export Excel Data to CSV (VBA)
```vb
Open "C:\data\july97\dr2.csv" For Output As #1
For I = 1 To 12
  For J = 1 to 6
    Print #1, Worksheets(1).Cells(I,J).Value; ",";
  Next
  Write #1,
Next
Close #1
```
**Note**: Exporting to CSV faster than opening Excel files directly from Prism.

### Generate PZC File from Excel (VBA)
```vb
Open "C:\Prism4\auto.pzc" For Output As #1
Print #1, "Shortlog"
Print #1, "SetPath C:\gp\prism25\"
Print #1, "Open generate.pzm"
Print #1, "openOutput fit.txt"
Print #1, "  ForEach 500"
Print #1, "    GoTo R 1"
Print #1, "    Regenerate"
Print #1, "    GoTo R 2"
Print #1, "    WCell 5,1"
Print #1, " Next"
Close #1
Shell ("C:\program files\prism5\prism.exe @C:\prism5\auto.pzc")
```

**Workflow**:
- All script commands in one Excel VBA file
- Each line prefaced with `Print #1,` and in quotes
- Simplifies editing (one file vs two)
- Trade-off: More complex syntax

### Import Prism Graph to Excel (VBA)
**Prerequisites**:
1. Manually create picture object in Excel (Control toolbar → Image button → drag)
2. Name the object (e.g., "DRGraph")

**VBA code**:
```vb
Sheet2.DRGraph.Picture = LoadPicture(Path$ + "dr2.wmf")
```

**Note**: Prism script must export graph as WMF first. No VBA command to directly import pictures, so must change existing picture object's contents.

## Key Concepts Summary

**File Extensions**:
- `.pzc` - Prism script file
- `.pzfx` - Prism XML format file (editable data)
- `.pzm` - Prism template/master file

**Script Execution Modes**:
1. **From Prism**: Interactive testing/development
2. **Desktop/Shortcuts**: User-initiated batch processing
3. **Shell/Automation**: Programmatic integration
4. **Intranet**: Web-based launching

**Cross-Platform Differences**:
- **Windows**: Shell, OLE/ActiveX, batch files
- **Mac**: Automator, MacScript, AppleScript (HFS/POSIX paths)

**Synchronization**:
- Shell/MacScript return immediately
- Use completion file pattern (done.txt) with polling loop
- OLE automation waits automatically

**No API**: Prism has no direct API - use scripts or PZFX file manipulation instead.
