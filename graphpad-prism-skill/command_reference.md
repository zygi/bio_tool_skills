# GraphPad Prism Script Commands - Quick Reference

## All Commands

**AnalyzeData** - Execute analysis

**ApplyColorScheme** ["scheme name"]

**Beep** - Play alert sound

**ClearTable** <row>,<col>,<subcol>

**Close** - Close current file

**CloseOutput** - Close output file

**CloseScriptDialog** - Close script dialog

**Copy** - Copy entire sheet to clipboard
**Copy** [#rows] [#columns] [first row] [first col] <first subcol>

**CreateLayout** [#rows] [#columns] <orientation> <#StartingGraphIndex> <#Graphs>

**CreateLog** - Create log file

**DeleteAllDataSets** <CLEAR>

**DuplicateDataTable** <CLEAR>

**DuplicateFamily** [prepend_text] <CLEAR>

**EndTable** - End table definition

**Exit** - Exit Prism (Windows only)

**ExportAllPDF** [filename] <mode> <CLEAR>

**ExportBMP** [filename] <mode><resolution>

**ExportEMF** [filename] <CLEAR> (Windows only)

**ExportEPS** [filename] <mode> <CLEAR> <nofonts>

**ExportJPG** [filename] <mode> <resolution>

**ExportPDF** [filename] <mode> <CLEAR>

**ExportPNG** [filename] <resolution><CLEAR>

**ExportSVG** [filename] <CLEAR>

**ExportTable** [filename] <ALL>

**ExportTIF** [filename] <resolution> <mode> <compress or LZW> <CLEAR>

**ExportWMF** [filename] <CLEAR> (Windows only)

**ForEach** [# of loops]
**ForEach** [filespec, i.e. "C:\data\DR*.txt"]

**ForEachSheet** - Loop over all sheets in current section

**GoTo** <section: D, I, R, G, or L>, <sheet#>, V<tab#>
**GoTo** + (next sheet)
**GoTo** - (previous sheet)

**Import** <"filename">, <row>, <col>, <subcol>

**ImportLink** <"filename">, <row>, <col>, <subcol>

**InsertData** <row>, <col>, <subcol>

**InsertDataLink** <row>, <col>, <subcol>

**Next** - End loop iteration

**NextRow** - Move to next table row

**Open** ["filename"]

**OpenOutput** ["filename"] ,<CLEAR>, <"delimiter">

**Paste** - Paste from clipboard at current position
**Paste** [first row] [first col] <first subcol>

**Print** - Print current sheet

**RecalcAll** - Recalculate all results

**Regenerate** - Regenerate random numbers (Monte Carlo)

**Save** <"filename">

**SendToPP** - Send to PowerPoint (Windows only)

**SendToWord** - Send to Word (Windows only)

**SetAxisLimits** [which axis: X, Y, or Y2] <segment #> [Top/Bottom/Interval] [value]

**SetAxisTitle** [which axis: X, Y, or Y2] ["new title"]

**SetGraphTitle** ["new title"]

**SetInfo** ["value"] [rownumber] <position: 0 (name) or 1 (value) or 2 (notes)>

**SetPath** ["drive and folder path"]

**SetProjectName** ["new name"]

**SetScriptDialogTitle** ["dialog title"]

**SetSheetTitle** [new_name] <K>

**SetValueToInfo** [infotable #] [inforow#] [datarow] [datacol] <datasubcol>

**SetYReplicates** [#]

**Table** Prism [datatable number] <CLEAR>
**Table** SQL [datasource] <table> <login> <password> <CLEAR>
**Table** XML [filename]

**WBlock** [row(s)] [column(s)] <row label> <;>

**WCell** [row], [col], <subcol> <;&>

**WCol** [row], <col>, <subcol><;&>

**WDate** <;&>

**WRow** [row], <col>, <subcol> <;&>

**WSheet** <row, col, subcol><;&>

**WTable** [column title] [row] [col] <subcol>
**WTable** [column title] [text or %F or %N]

**WText** <"text"> <;&>

**WTBlock** [row(s)] [columns to include] <row label> <;>

**WTCol** [row, col] <, subcolumn><;&>

**WTitle** - Write sheet title

**WTRow** [row] <,col, subcol> <;&>

**WTSheet** <row, col, subcol><;&>

**WVersion** - Write Prism version

## Variables

%N - Loop iteration number
%F - Filename (without extension)
%X - File extension
%S - Sheet name
%D - Date and time file was last saved
%P - Prism project filename
%% - Literal percent sign

## Syntax Notation

[required] - Required parameter
<optional> - Optional parameter
<;&> - Optional semicolon (continue on same line with tab) or ampersand (continue without tab)
<;> - Optional semicolon (joins text without delimiter)
CLEAR - Keyword to clear existing content

## Section Codes

D - Data tables
I - Info tables
R - Results tables
G - Graphs
L - Layouts

## Column/Row Numbering

Columns: -1 (Row titles), 0 (X column), 1 (Column A), 2 (Column B)...
Rows: 0 or -1 (Column titles), 1 (First data row), 2 (Second data row)...

## Data Block Syntax

<DATA>
value1	value2	value3
value4	value5	value6
</DATA>

## Comments

// Single line comment
/* Multi-line comment */
