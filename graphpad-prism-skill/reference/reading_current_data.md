# Reading Current Prism Data State

## The Problem

Prism scripts **cannot read data values** into variables or use them for conditional logic. This is a fundamental limitation of the scripting language.

**What you CANNOT do:**
- Read a cell value into a variable
- Use if/else conditionals based on data
- Make script decisions based on analysis results
- Loop until a condition is met
- Compare values or perform calculations in the script

**The only "read" command** is `SetValueToInfo`, which copies a value from one location to another but doesn't let you use it in logic.

## The Solution: PZFX XML Files

Since Prism's scripting language can't read data, use the **PZFX file format** instead.

### What is PZFX?

- **PZFX** = Prism's XML file format
- Plain text XML structure (human and machine readable)
- **Data tables**: Fully readable and editable in XML
- **Info tables**: Fully readable and editable in XML
- **Analysis results**: Encrypted but readable as XML
- **Graph/analysis settings**: Encrypted (not editable externally)

### How Prism Automation Works

```
Prism's Built-in Automation:
  Data → Auto-updates → Graphs
  Data → Auto-updates → Analyses
  Data → Auto-updates → Paste-linked tables
```

When you edit data in a PZFX file externally, Prism sees it as a data change and **auto-updates everything**.

## Workflow Patterns

### Pattern 1: Read-Only Analysis (Python/R)

**Use case**: Extract data/results for external analysis

```python
# Python example
import xml.etree.ElementTree as ET

# Read PZFX file
tree = ET.parse('experiment.pzfx')
root = tree.getroot()

# Extract data from table
for table in root.findall('.//Table'):
    table_name = table.get('ID')
    # Parse data cells
    for cell in table.findall('.//d'):
        value = cell.text
        # Analyze, plot, whatever you need
```

### Pattern 2: Modify Data, Let Prism Analyze

**Use case**: Pre-process data externally, let Prism do analysis/graphing

```python
# Python example
import xml.etree.ElementTree as ET

# 1. Read PZFX template
tree = ET.parse('template.pzfx')

# 2. Modify data in XML
for table in tree.findall('.//Table[@ID="Data 1"]'):
    # Update cell values
    cells = table.findall('.//d')
    cells[0].text = str(new_value)

# 3. Save modified file
tree.write('processed.pzfx')

# 4. Open in Prism - graphs/analyses auto-update!
```

### Pattern 3: Conditional Logic + Prism Script

**Use case**: Make decisions based on data, then run appropriate Prism script

```python
# Python: Read data and decide what to do
tree = ET.parse('results.pzfx')
p_value = float(tree.find('.//Cell[@Row="5"][@Col="3"]').text)

if p_value < 0.05:
    script_to_run = "significant.pzc"
else:
    script_to_run = "not_significant.pzc"

# Launch appropriate Prism script
import subprocess
subprocess.run(['prism', script_to_run])
```

### Pattern 4: Full Integration (Python + Prism)

**Complete workflow**:

```python
#!/usr/bin/env python3
import xml.etree.ElementTree as ET
import subprocess
import os

# 1. Prepare data externally
data = process_experimental_data()

# 2. Create/modify PZFX file
tree = ET.parse('template.pzfx')
# ... populate data in XML ...
tree.write('experiment.pzfx')

# 3. Run Prism script to generate graphs
subprocess.run(['prism', '@export_graphs.pzc'])

# 4. Read results back from PZFX
tree = ET.parse('experiment.pzfx')
results = extract_results(tree)

# 5. Make decisions, generate reports
if results['significant']:
    generate_report(results)
```

## Platform-Specific Integration

### macOS Approach

**Problem**: AppleScript can only launch Prism scripts, not read data.

**Solution**: Use PZFX + external tool

```applescript
-- Save current work (user does this manually or via script)
tell application "Prism"
    -- Prism must already have file open, saved as PZFX
end tell

-- Process with Python/R/etc
do shell script "python3 ~/scripts/analyze_prism.py"

-- Reopen in Prism (optional - user can do manually)
tell application "Prism"
    activate
    open POSIX file "/path/to/processed.pzfx"
end tell
```

### Windows Approach

**Same principle**, but can use VBA, PowerShell, or Python:

```vbnet
' Excel VBA example
Sub ProcessPrismData()
    ' 1. Tell Prism to save as PZFX
    '    (user must do manually or via Save command in script)

    ' 2. Parse XML (using MSXML2.DOMDocument)
    Dim xmlDoc As Object
    Set xmlDoc = CreateObject("MSXML2.DOMDocument")
    xmlDoc.Load "C:\Data\experiment.pzfx"

    ' 3. Read values
    Dim pValue As Double
    pValue = CDbl(xmlDoc.SelectSingleNode("//Cell[@Row='5']").Text)

    ' 4. Make decision
    If pValue < 0.05 Then
        Shell "C:\Prism\prism.exe @C:\Scripts\significant.pzc"
    End If
End Sub
```

## XML Structure Reference

### Data Table Structure
```xml
<Table ID="Data 1" XFormat="none" YFormat="replicates">
    <Title>Dose Response</Title>
    <ColumnTitlesRow>
        <d>Dose</d>
        <d>Response</d>
    </ColumnTitlesRow>
    <YColumn>
        <Title>Control</Title>
        <Subcolumn>
            <d>10.5</d>
            <d>12.3</d>
            <d>11.8</d>
        </Subcolumn>
    </YColumn>
</Table>
```

### Info Constants Structure
```xml
<InfoSequence>
    <Ref ID="Info0" />
</InfoSequence>

<Info ID="Info0">
    <Title>Experiment Details</Title>
    <Constant>
        <Name>Date</Name>
        <Value>2024-01-15</Value>
    </Constant>
    <Constant>
        <Name>Experimenter</Name>
        <Value>Jane Smith</Value>
    </Constant>
</Info>
```

## Complete Example: Batch Processing with Conditionals

```python
#!/usr/bin/env python3
"""
Process multiple data files through Prism with conditional logic.
Prism scripts can't do conditionals, so we use Python + PZFX.
"""

import xml.etree.ElementTree as ET
import subprocess
import glob

# 1. Process each data file
for data_file in glob.glob('data/*.csv'):
    # 2. Read data to make decision
    data = read_csv(data_file)

    # 3. Choose appropriate Prism template based on data
    if len(data) > 100:
        template = 'large_dataset_template.pzfx'
    else:
        template = 'small_dataset_template.pzfx'

    # 4. Load template and populate data
    tree = ET.parse(template)
    populate_prism_data(tree, data)

    # 5. Save as new PZFX file
    output_file = f'processed/{data_file}.pzfx'
    tree.write(output_file)

    # 6. Run Prism script to export graphs
    # Script is simple: just exports graphs, no logic needed
    subprocess.run(['prism', '@export_graphs.pzc', output_file])

    # 7. Read results back if needed
    tree = ET.parse(output_file)
    results = extract_results(tree)

    # 8. Store in database, generate report, etc.
    save_to_database(results)
```

## Key Takeaways

1. **Prism scripts are write-only**: They can import/export but not read for logic
2. **PZFX is the read interface**: Use external tools to parse XML
3. **Prism auto-updates**: Changing data in PZFX updates graphs/analyses
4. **Hybrid approach**: External tool for logic, Prism script for automation
5. **XML is documented**: Use the schema files in `prism-xml-schema/` directory

## Tools for PZFX Processing

### Python
- `xml.etree.ElementTree` - Built-in XML parser
- `lxml` - More powerful XML processing
- `pandas` - Can read XML tables

### R
- `xml2` package - XML parsing
- `XML` package - Alternative parser

### Excel VBA
- `MSXML2.DOMDocument` - Microsoft's XML parser

### Command Line
- `xmllint` - Validate and query XML
- `xq` - jq for XML (requires installation)

## When to Use Each Approach

| Scenario | Best Approach |
|----------|---------------|
| Simple batch import | Prism script alone |
| Conditional processing | Python/R + PZFX |
| Read analysis results | Python/R + PZFX |
| Complex data prep | External tool → PZFX → Prism |
| Integration with LIMS | Python/R reads/writes PZFX |
| Decision trees | External tool, not Prism script |
