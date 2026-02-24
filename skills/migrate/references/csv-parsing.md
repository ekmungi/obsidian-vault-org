# CSV Parsing Reference

Patterns and utilities for reading Notion export CSVs in different environments.

---

## Notion Export Format

Notion exports each database as a separate `.csv` file. Common column name patterns:

| Database type | Key columns |
|--------------|-------------|
| Tasks | `Task name`, `Status`, `Priority`, `Due Date`, `Project`, `Assignee` |
| Projects | `Project name`, `Status`, `Priority`, `Products`, `Start Date`, `Due Date` |
| Products | `Name`, `Status`, `Area`, `Partner`, `PM` |
| Versions | `Version name`, `Product`, `Status`, `Target release`, `Actual release` |
| Stakeholders | `Name`, `Role`, `Organization`, `Team`, `Products` |

Column names may vary. Always read the header row first and confirm mapping with the user
before processing rows.

---

## Notion Relation Field Format

Notion exports relation fields (linked database references) in this format:

```
Product Name (path/to/database_id.csv)
```

For multi-value relations, values are comma-separated:

```
Product A (path/id.csv), Product B (path/id.csv)
```

### Extraction Regex

Strip path data and extract clean names using: `^([^(]+?)(?:\s*\(|\s*$)`

- Group 1 captures the clean name before the first `(` or end of string
- Works for both single and multi-value fields
- Trim whitespace from the result

---

## PowerShell CSV Parsing (Windows Enterprise)

On Windows enterprise machines, Python is often unavailable. Use PowerShell.

**Critical rule:** Never pass multi-line PowerShell inline through bash. Always write the
script to a temp `.ps1` file first, then execute it.

### Template: Write script to file, then run

```bash
# Step 1: Write the PowerShell script using the Write tool
# Path: C:/Users/[user]/AppData/Local/Temp/parse.ps1

# Step 2: Execute
powershell.exe -ExecutionPolicy Bypass -File 'C:/Users/[user]/AppData/Local/Temp/parse.ps1'
```

### Template: Basic CSV summary script

```powershell
# parse_summary.ps1
$csvPath = 'C:/path/to/export.csv'
$rows = Import-Csv $csvPath

$byStatus = @{}
$byProject = @{}

foreach ($row in $rows) {
    $name   = $row.'Task name'
    $status = $row.Status

    # Strip Notion relation format: "Name (path/id.csv)" -> "Name"
    $rawProject = $row.Project
    $projName = ''
    if ($rawProject -match '^([^(]+?)(?:\s*\(|\s*$)') {
        $projName = $matches[1].Trim()
    }
    if ($projName -eq '') { $projName = '(no project)' }

    # Accumulate counts
    if (-not $byStatus.ContainsKey($status)) { $byStatus[$status] = 0 }
    $byStatus[$status]++

    if (-not $byProject.ContainsKey($projName)) { $byProject[$projName] = @() }
    $byProject[$projName] += "$status | $name"
}

Write-Host "=== TOTAL: $($rows.Count) rows ==="
Write-Host ""
Write-Host "=== BY STATUS ==="
foreach ($k in ($byStatus.Keys | Sort-Object)) {
    Write-Host ("  {0,3}  {1}" -f $byStatus[$k], $k)
}
Write-Host ""
Write-Host "=== BY PROJECT ==="
foreach ($proj in ($byProject.Keys | Sort-Object)) {
    Write-Host "  [$($byProject[$proj].Count)]  $proj"
    foreach ($t in $byProject[$proj]) {
        Write-Host "        - $t"
    }
}
```

### Template: Multi-value relation extraction

```powershell
# Extract multiple values from a Notion relation field
# Input:  "Product A (path/a.csv), Product B (path/b.csv)"
# Output: @("Product A", "Product B")

function Extract-NotionRelations {
    param([string]$raw)
    $results = @()
    $matches = [regex]::Matches($raw, '([^,(]+?)(?=\s*\(|\s*,|\s*$)')
    foreach ($m in $matches) {
        $val = $m.Groups[1].Value.Trim()
        if ($val -ne '') { $results += $val }
    }
    return $results
}

# Usage
$products = Extract-NotionRelations $row.Products
```

---

## Column Mapping Guide

When reading an unfamiliar export, map CSV columns to vault schema properties:

| CSV column | Vault property | Notes |
|-----------|---------------|-------|
| `Task name` | `title` (filename) | Use as folder name and h1 heading |
| `Status` | `status:` | Map to vault status vocabulary |
| `Priority` | `priority:` | Map to vault priority vocabulary |
| `Due Date` | `due-date:` | Convert to YYYY-MM-DD |
| `Project` | `project:` | Extract name, wrap in `[[...]]` |
| `Assignee` | `owner:` | Map to stakeholder note name |
| `Tags` | `tags:` | May need cleaning (spaces → hyphens) |

### Status Vocabulary Mapping

Notion status values vary by workspace. Map to vault vocabulary before writing:

| Common Notion value | Vault value |
|--------------------|-------------|
| Not started | `todo` |
| In progress | `in-progress` |
| In review | `review` |
| Done, Complete | `done` |
| Canceled | skip (or `done` if content is useful) |
| On hold, Paused | `blocked` or `delayed` |
| Delayed | `delayed` |

---

## Large CSV Handling

For CSVs with 200+ rows, reading the full file at once may exceed output limits. Use
targeted extraction:

```powershell
# Read only specific columns to reduce output size
$rows = Import-Csv $csvPath | Select-Object 'Task name', 'Status', 'Project'
```

Or filter to a specific project before displaying:

```powershell
$rows = Import-Csv $csvPath | Where-Object { $_.Project -like '*Project A*' }
```
