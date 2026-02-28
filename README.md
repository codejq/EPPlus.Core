```
╔══════════════════════════════════════════════════════════════════╗
║                                                                  ║
║   ███████╗██████╗ ██████╗ ██╗     ██╗   ██╗███████╗            ║
║   ██╔════╝██╔══██╗██╔══██╗██║     ██║   ██║██╔════╝            ║
║   █████╗  ██████╔╝██████╔╝██║     ██║   ██║███████╗            ║
║   ██╔══╝  ██╔═══╝ ██╔═══╝ ██║     ██║   ██║╚════██║            ║
║   ███████╗██║     ██║     ███████╗╚██████╔╝███████║            ║
║   ╚══════╝╚═╝     ╚═╝     ╚══════╝ ╚═════╝ ╚══════╝            ║
║                                                                  ║
║        C O R E  ·  C O R E C O M P A T                         ║
║        Excel for modern .NET — without the pain                 ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

[![NuGet](https://img.shields.io/nuget/v/EPPlus.Core.CoreCompat.svg?label=NuGet&color=004880)](https://www.nuget.org/packages/EPPlus.Core.CoreCompat)
[![NuGet Downloads](https://img.shields.io/nuget/dt/EPPlus.Core.CoreCompat.svg?color=004880)](https://www.nuget.org/packages/EPPlus.Core.CoreCompat)
[![Build](https://github.com/codejq/EPPlus.Core/actions/workflows/build.yml/badge.svg)](https://github.com/codejq/EPPlus.Core/actions/workflows/build.yml)
[![License: LGPL v2.1](https://img.shields.io/badge/License-LGPL%20v2.1-blue.svg)](LICENSE.md)
[![.NET](https://img.shields.io/badge/.NET-Standard%202.0-512BD4)](https://www.nuget.org/packages/EPPlus.Core.CoreCompat)
[![Docs](https://img.shields.io/badge/docs-GitHub%20Pages-brightgreen)](https://codejq.github.io/EPPlus.Core/)

---

## Why You Need This

The original **EPPlus** library is locked to .NET Framework. Later forks added .NET Core support via the `CoreCompat.System.Drawing` shim — but that package is abandoned and broken on modern runtimes.

**EPPlus.Core.CoreCompat** is the community-maintained drop-in that works today:

```
  Legacy world                        This package
  ────────────                        ────────────
  EPPlus (full .NET only)             ✔  .NET Standard 2.0
  EPPlus.Core (CoreCompat shim)  →       Works on Windows, Linux, macOS
  VahidN/EPPlus.Core (abandoned)         No Windows-only lock-in
```

> .NET Standard 2.0 — cross-platform by design. Runs on .NET Core, .NET Framework, Mono, Unity, Xamarin.

---

## What You Can Do

```
EPPlus.Core.CoreCompat
│
├── Workbooks & Worksheets
│   ├── Create / open / save .xlsx files
│   ├── Multiple sheets, named ranges, freeze panes
│   └── Worksheet protection & workbook encryption
│
├── Cells & Ranges
│   ├── Read / write values, formulas, hyperlinks
│   ├── Merge cells, auto-fit columns & rows
│   └── Copy / move ranges between sheets
│
├── Styles & Formatting
│   ├── Fonts  (name, size, bold, italic, color, underline)
│   ├── Fills  (solid, gradient, pattern)
│   ├── Borders (style, color, all sides)
│   ├── Number formats (dates, currency, custom)
│   └── Conditional formatting rules
│
├── Charts  (embedded in the worksheet)
│   ├── Line, Bar, Column, Area
│   ├── Pie, Doughnut, Scatter, Bubble
│   └── Combination charts & chart series
│
├── Tables & Pivot Tables
│   ├── Excel ListObject tables with auto-filter
│   └── PivotTable with grouping & calculated fields
│
├── Formulas
│   ├── 300+ built-in Excel functions evaluated server-side
│   └── Array formulas & cross-sheet references
│
└── Data Import / Export
    ├── Load from IEnumerable<T> / DataTable
    └── Export to CSV, print-ready layout
```

---

## Install

```bash
dotnet add package EPPlus.Core.CoreCompat
```

or in the Package Manager Console:

```powershell
Install-Package EPPlus.Core.CoreCompat
```

---

## Quick Examples

### Create a workbook

```csharp
using OfficeOpenXml;
using System.IO;

// No license key needed for this community fork
ExcelPackage.LicenseContext = LicenseContext.NonCommercial;

using var package = new ExcelPackage();
var sheet = package.Workbook.Worksheets.Add("Sales");

// Headers
sheet.Cells[1, 1].Value = "Product";
sheet.Cells[1, 2].Value = "Q1";
sheet.Cells[1, 3].Value = "Q2";
sheet.Cells[1, 4].Value = "Total";

// Data rows
string[][] rows = [["Widget A", "1200", "1500"], ["Widget B", "800", "950"]];
for (int r = 0; r < rows.Length; r++)
{
    sheet.Cells[r + 2, 1].Value = rows[r][0];
    sheet.Cells[r + 2, 2].Value = int.Parse(rows[r][1]);
    sheet.Cells[r + 2, 3].Value = int.Parse(rows[r][2]);
    sheet.Cells[r + 2, 4].Formula = $"B{r+2}+C{r+2}";
}

// Style the header row
using (var header = sheet.Cells[1, 1, 1, 4])
{
    header.Style.Font.Bold = true;
    header.Style.Fill.PatternType = ExcelFillStyle.Solid;
    header.Style.Fill.BackgroundColor.SetColor(Color.DarkBlue);
    header.Style.Font.Color.SetColor(Color.White);
}

sheet.Cells.AutoFitColumns();
package.SaveAs(new FileInfo("sales.xlsx"));
```

### Read an existing file

```csharp
using var package = new ExcelPackage(new FileInfo("sales.xlsx"));
var sheet = package.Workbook.Worksheets[0];

int rows = sheet.Dimension.Rows;
for (int r = 2; r <= rows; r++)
{
    Console.WriteLine($"{sheet.Cells[r,1].Value}  Q1={sheet.Cells[r,2].Value}");
}
```

### Load from a list

```csharp
var data = new List<Order>
{
    new() { Id = 1, Product = "Widget A", Amount = 120.50m },
    new() { Id = 2, Product = "Widget B", Amount = 89.99m },
};

sheet.Cells["A1"].LoadFromCollection(data, printHeaders: true);
```

### Add a chart

```csharp
var chart = sheet.Drawings.AddChart("SalesChart", eChartType.ColumnClustered);
chart.Title.Text = "Quarterly Sales";
chart.SetPosition(5, 0, 1, 0);
chart.SetSize(600, 300);
var series = chart.Series.Add(sheet.Cells["B2:C3"], sheet.Cells["A2:A3"]);
series.Header = "Revenue";
```

---

## Framework Support

| Target               | Status  | Compatible runtimes                                              |
|----------------------|---------|------------------------------------------------------------------|
| .NET Standard 2.0    | ✔ Full  | .NET Core 2.0+, .NET 5/6/7/8/9, .NET Framework 4.6.1+, Mono    |

**Cross-platform by design.** The `netstandard2.0` target means one binary runs everywhere — Windows, Linux, and macOS — without any platform-specific lock-in.

---

## License

Licensed under the [GNU Lesser General Public License v2.1](LICENSE.md).
This is a community fork. The original EPPlus project lives at [JanKallman/EPPlus](https://github.com/JanKallman/EPPlus).
