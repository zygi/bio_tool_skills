# Prism XML File Format & Schemas

## Overview

GraphPad Prism uses XML-based file formats that allow external programs to read and modify data without using Prism's limited scripting language.

## File Formats

### PZFX - Prism XML Format
- **Extension**: `.pzfx`
- **Format**: Plain text XML
- **Readable**: Yes - open in any text editor
- **Data tables**: Fully accessible as XML
- **Info tables**: Fully accessible as XML
- **Analysis results**: Encrypted but readable as XML
- **Graphs/settings**: Encrypted (not editable externally)
- **Use case**: Primary format for external data access

### PZF - Prism Binary Format
- **Extension**: `.pzf`
- **Format**: Binary (not human-readable)
- **Use case**: Smaller file size, faster loading
- **External access**: None - must convert to PZFX first

### PZM - Prism Template/Master
- **Extension**: `.pzm`
- **Format**: Can be PZFX or PZF
- **Use case**: Templates for repeated analysis
- **Scripting**: Open with `Open "template.pzm"`

### PZC - Prism Script
- **Extension**: `.pzc`
- **Format**: Plain text script commands
- **Use case**: Automation scripts

## Official XML Schemas

GraphPad provides official XML schemas for Prism 7.0+ format.

**Schema files included in this skill:**
- `Prism7XMLSchema.xml` - Complete schema definition
- `Prism7XMLStyleSheet.xml` - XSLT for transforming Prism XML

**Schema location**: `../../prism-xml-schema/`

**Schema covers**:
- Data table structure
- Info table structure
- Formatting elements (fonts, colors, alignment)
- Metadata (version, creation date, user info)
- Text formatting (bold, italic, underline, super/subscript)

## PZFX File Structure

### Root Structure
```xml
<?xml version="1.0" encoding="UTF-8"?>
<GraphPadPrismFile>
    <OriginalVersion CreatedByProgram="Prism" CreatedByVersion="10.0.0" />
    <TableSequence>
        <Ref ID="Table0" />
        <Ref ID="Table1" />
    </TableSequence>
    <Table ID="Table0" ...>
        <!-- Data table content -->
    </Table>
    <InfoSequence>
        <Ref ID="Info0" />
    </InfoSequence>
    <Info ID="Info0" ...>
        <!-- Info constants -->
    </Info>
</GraphPadPrismFile>
```

### Data Table Elements

#### Basic Data Table
```xml
<Table ID="Data 1" XFormat="none" YFormat="replicates" Replicates="3">
    <Title>Dose Response Data</Title>

    <!-- Column titles -->
    <ColumnTitlesRow>
        <d>X</d>
        <d>Y</d>
    </ColumnTitlesRow>

    <!-- X Column -->
    <XColumn Width="81">
        <d>0.1</d>
        <d>1.0</d>
        <d>10.0</d>
    </XColumn>

    <!-- Y Column with replicates -->
    <YColumn Width="81">
        <Title>Control</Title>
        <Subcolumn>
            <d>10.5</d>
            <d>12.3</d>
            <d>11.8</d>
        </Subcolumn>
    </YColumn>

    <YColumn Width="81">
        <Title>Treatment</Title>
        <Subcolumn>
            <d>8.2</d>
            <d>9.1</d>
            <d>8.7</d>
        </Subcolumn>
    </YColumn>
</Table>
```

#### Table Attributes
- `ID` - Unique identifier (e.g., "Data 1", "Table0")
- `XFormat` - X column format: "none", "numbers", "text", "date"
- `YFormat` - Y format: "replicates", "SD", "SEM", "mean"
- `Replicates` - Number of subcolumns per Y column
- `RowTitlesWidth` - Width of row titles column

### Info Table Elements

```xml
<Info ID="Info0">
    <Title>Experiment Information</Title>

    <!-- Individual constants -->
    <Constant>
        <Name>Date</Name>
        <Value>2024-01-15</Value>
    </Constant>

    <Constant>
        <Name>Experimenter</Name>
        <Value>Jane Smith</Value>
    </Constant>

    <Constant>
        <Name>Concentration</Name>
        <Value>10.5</Value>
    </Constant>

    <!-- Notes section -->
    <Notes>
        Experiment notes go here.
        Multiple lines allowed.
    </Notes>
</Info>
```

### Results Tables (Encrypted)

```xml
<HugeResults ID="HugeResults0">
    <!-- Results are encrypted but structure is visible -->
    <OriginalVersion ... />
    <AnalysisParams ...>
        <!-- Some parameters visible -->
    </AnalysisParams>
    <!-- Actual results encrypted -->
</HugeResults>
```

**Note**: Results are readable as XML but values are encrypted. You can see structure but not values without Prism.

### Data Types

#### Numeric Values
```xml
<d>10.5</d>          <!-- Regular number -->
<d>1.23e-4</d>       <!-- Scientific notation -->
<d></d>              <!-- Missing/empty value -->
```

#### Text Values
```xml
<d>Sample A</d>      <!-- Plain text -->
<d>Control</d>       <!-- Text in row/column titles -->
```

#### Formatted Text
```xml
<d>
    <b>Bold text</b>
    <i>Italic text</i>
    <u>Underlined</u>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
</d>
```

#### Dates
```xml
<Constant>
    <Name>ExperimentDate</Name>
    <Value>2024-01-15</Value>
</Constant>
```

## Parsing PZFX Files

### Python Example

```python
import xml.etree.ElementTree as ET

# Parse file
tree = ET.parse('experiment.pzfx')
root = tree.getroot()

# Find all data tables
for table in root.findall('.//Table'):
    table_id = table.get('ID')
    title = table.find('Title').text

    print(f"Table: {table_id} - {title}")

    # Get X values
    x_column = table.find('XColumn')
    if x_column is not None:
        x_values = [d.text for d in x_column.findall('d')]
        print(f"  X values: {x_values}")

    # Get Y values
    for y_column in table.findall('YColumn'):
        y_title = y_column.find('Title').text
        subcolumn = y_column.find('Subcolumn')
        y_values = [d.text for d in subcolumn.findall('d')]
        print(f"  {y_title}: {y_values}")
```

### Reading Info Constants

```python
# Find info tables
for info in root.findall('.//Info'):
    info_id = info.get('ID')
    print(f"Info table: {info_id}")

    # Get all constants
    for constant in info.findall('Constant'):
        name = constant.find('Name').text
        value = constant.find('Value').text
        print(f"  {name}: {value}")

    # Get notes
    notes = info.find('Notes')
    if notes is not None:
        print(f"  Notes: {notes.text}")
```

### Modifying Data

```python
# Load template
tree = ET.parse('template.pzfx')

# Find first data table
table = tree.find('.//Table[@ID="Data 1"]')

# Modify X values
x_column = table.find('XColumn')
x_cells = x_column.findall('d')
x_cells[0].text = "0.5"
x_cells[1].text = "5.0"

# Modify Y values
y_column = table.find('YColumn')
subcolumn = y_column.find('Subcolumn')
y_cells = subcolumn.findall('d')
y_cells[0].text = "15.2"

# Save modified file
tree.write('modified.pzfx', encoding='UTF-8', xml_declaration=True)
```

## XPath Query Examples

### Find specific table
```python
table = root.find(".//Table[@ID='Data 1']")
```

### Find all Y columns
```python
y_columns = root.findall(".//Table[@ID='Data 1']/YColumn")
```

### Find specific info constant
```python
date = root.find(".//Info/Constant[Name='Date']/Value").text
```

### Count data points
```python
num_points = len(root.findall(".//Table[@ID='Data 1']/XColumn/d"))
```

## Validation

### Using xmllint (command line)
```bash
# Validate against schema
xmllint --schema Prism7XMLSchema.xml experiment.pzfx

# Pretty print
xmllint --format experiment.pzfx

# Extract specific elements
xmllint --xpath "//Table/@ID" experiment.pzfx
```

### Python Validation
```python
from lxml import etree

# Load schema
schema_doc = etree.parse('Prism7XMLSchema.xml')
schema = etree.XMLSchema(schema_doc)

# Validate file
doc = etree.parse('experiment.pzfx')
is_valid = schema.validate(doc)

if not is_valid:
    print(schema.error_log)
```

## Common Patterns

### Extract all data to CSV
```python
import csv

tree = ET.parse('experiment.pzfx')

for table in tree.findall('.//Table'):
    table_id = table.get('ID')

    with open(f'{table_id}.csv', 'w', newline='') as csvfile:
        writer = csv.writer(csvfile)

        # Write column titles
        titles = [d.text for d in table.find('ColumnTitlesRow').findall('d')]
        writer.writerow(titles)

        # Write data rows
        x_column = table.find('XColumn')
        x_values = [d.text for d in x_column.findall('d')]

        for i, x_val in enumerate(x_values):
            row = [x_val]
            for y_column in table.findall('YColumn'):
                subcolumn = y_column.find('Subcolumn')
                y_values = [d.text for d in subcolumn.findall('d')]
                row.append(y_values[i] if i < len(y_values) else '')
            writer.writerow(row)
```

### Batch update info constants
```python
import glob

for pzfx_file in glob.glob('*.pzfx'):
    tree = ET.parse(pzfx_file)

    # Find or create date constant
    info = tree.find('.//Info')
    date_constant = info.find("./Constant[Name='Date']")

    if date_constant is None:
        # Create new constant
        constant = ET.SubElement(info, 'Constant')
        ET.SubElement(constant, 'Name').text = 'Date'
        ET.SubElement(constant, 'Value').text = '2024-01-15'
    else:
        # Update existing
        date_constant.find('Value').text = '2024-01-15'

    tree.write(pzfx_file, encoding='UTF-8', xml_declaration=True)
```

## Limitations

**What you CAN access**:
- ✅ All data values in tables
- ✅ All info constants
- ✅ Table structure and format
- ✅ Column/row titles
- ✅ File metadata

**What you CANNOT access**:
- ❌ Analysis parameter details (encrypted)
- ❌ Calculated results values (encrypted)
- ❌ Graph appearance settings (encrypted)
- ❌ Analysis method details (encrypted)

**Workaround**: Let Prism do the analysis. You modify data, Prism recalculates everything when opened.

## Best Practices

1. **Always preserve XML declaration**: Keep `<?xml version="1.0"?>` header
2. **Maintain structure**: Don't remove or reorder major elements
3. **Validate after modification**: Use xmllint or schema validation
4. **Backup originals**: Keep copy before programmatic modification
5. **Use proper encoding**: Save as UTF-8 with XML declaration
6. **Handle missing values**: Empty `<d></d>` tags for missing data
7. **Test with Prism**: Open modified files in Prism to verify

## Resources

- **Schema files**: `../../prism-xml-schema/Prism7XMLSchema.xml`
- **Stylesheet**: `../../prism-xml-schema/Prism7XMLStyleSheet.xml`
- **GraphPad documentation**: https://www.graphpad.com/
- **XML tools**: xmllint, Python lxml, R xml2 package
