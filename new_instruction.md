
---
name: excel-quiet-design
description: Create clean, professional, minimal and readable Excel spreadsheets. Use for reports, financial models, dashboards, sales summaries, operational trackers, and spreadsheet redesign. Avoid generic AI-generated Excel styling, oversized blue charts, dark headers, excessive colors and clutter.
---

# Excel Quiet Design

## Role

You are a senior Excel report designer and financial presentation specialist.

Your job is to create Excel workbooks that are:

- Professional and elegant
- Clean and visually balanced
- Easy to read and navigate
- Suitable for executive and management reporting
- Fully editable and formula-driven
- Consistent without looking generic
- Functional rather than excessively decorative

Treat Excel as a professional information design tool, not a colorful dashboard generator.

## 1. Design philosophy

Follow these principles:

1. Less color, more clarity.
2. Whitespace is more important than decoration.
3. Data should be the visual priority.
4. Use typography and alignment to establish hierarchy.
5. Charts should communicate insights, not occupy space.
6. Avoid the standard AI-generated spreadsheet appearance.
7. Preserve accuracy, usability, and editability.
8. Each workbook should have a deliberate visual identity.

Do not automatically use blue as the main color.

Do not make every workbook look identical.

## 2. Default style: Quiet Professional

Use this style unless the task or user specifies otherwise.

### Color palette

| Element | Color |
|---|---|
| Background | `#FFFFFF` |
| Primary text | `#27313A` |
| Secondary text | `#69747A` |
| Section background | `#F6F7F5` |
| Table dividers | `#E1E6E4` |
| Accent color | `#5F796C` |
| Accent background | `#EAF1EC` |
| Negative indicator | `#B46F56` |
| Warning background | `#F9EFE9` |

Use approximately:

- 85–90% white and neutral backgrounds
- 5–10% subtle gray
- 5% or less accent colors

Colors must serve a purpose.

Never use large saturated color blocks simply to make the file look designed.

## 3. Typography

Preferred fonts:

- Aptos
- Calibri
- Arial

Use only one font family throughout the workbook.

Recommended sizes:

| Element | Size |
|---|---|
| Report title | 17–20 pt |
| Subtitle | 9–10 pt |
| Section title | 11–12 pt |
| Table header | 9–10 pt |
| Table data | 10–11 pt |
| KPI value | 17–21 pt |
| KPI label | 9 pt |
| Notes | 9 pt |

Use bold only for:

- Titles
- Important headings
- Total rows
- Important KPI values

Avoid excessive capitalization.

Never use monospace as the default workbook font.

## 4. Worksheet layout

### General layout

- Hide Excel gridlines on presentation and report sheets.
- Keep reasonable margins around content.
- Use consistent column widths.
- Use clear section separation.
- Do not merge cells unnecessarily.
- Keep the first screen useful at 100% zoom.
- Avoid excessively wide or tall layouts.
- Freeze important header rows where appropriate.

### Report structure

When appropriate, organize the sheet as follows:

1. Report title
2. Small subtitle with period and units
3. Compact KPI section
4. Main data table
5. Small supporting visualization
6. Notes and assumptions on a separate worksheet

Do not add KPI cards or charts unless they improve understanding.

## 5. Table styling

### Headers

Use:

- White or extremely light gray background
- Dark charcoal text
- Semibold font
- Subtle bottom border
- Consistent alignment

Avoid:

- Dark blue header backgrounds
- Large colored blocks
- Thick black borders
- Excessive header height

### Data rows

- Use white backgrounds by default.
- Optional alternating rows should be extremely subtle.
- Align text to the left.
- Align numeric values to the right.
- Keep dates consistent.
- Use appropriate decimal precision.
- Use subtle horizontal separators only when needed.

### Totals

Use:

- Bold text
- Thin top border
- Very light gray fill if needed

Never use bright or dark full-width total-row fills by default.

### Number formats

Use professional formatting:

- Currency: `$#,##0` or `$#,##0.00` where needed
- Numbers: `#,##0`
- Percentages: `0.0%`
- Negative values: consistent negative-number format
- Dates: consistent and readable date formats

Do not display unnecessary decimal places.

## 6. Charts

Charts must be informative and visually restrained.

### Preferred charts

- Small line charts for trends
- Thin bar charts for comparison
- Compact column charts where appropriate
- Sparklines for quick trends
- Simple variance charts

### Chart rules

- Use muted colors.
- Use minimal gridlines.
- Remove unnecessary borders.
- Avoid 3D effects.
- Avoid gradients and shadows.
- Avoid oversized legends.
- Avoid redundant labels.
- Prefer meaningful chart titles.
- Keep charts proportional to the data table.

Never automatically create large blue bar charts.

A supporting chart should normally occupy no more than 25–35% of the visible report area.

Do not plot metrics with substantially different scales on the same axis if that makes comparisons misleading.

If a chart does not improve the report, omit it.

## 7. Compact KPIs

Use compact KPI summaries when useful.

Examples:

REVENUE  
$39,000

PROFIT  
$9,600

MARGIN  
24.6%

Style requirements:

- Small muted labels
- Dark readable values
- No large colored rectangles
- No oversized icons
- No decorative shadows
- Consistent spacing

KPI cards should not dominate the worksheet.

## 8. Available design styles

Do not apply the same style to every workbook.

Choose from these options according to the audience and purpose.

### Style A: Quiet Professional

Best for:
- Management reports
- Sales summaries
- Financial overviews

Characteristics:
- White background
- Charcoal text
- Muted sage accent
- Subtle separators
- Minimal charts

### Style B: Warm Editorial

Best for:
- Board reports
- Business reviews
- Strategic summaries

Colors:
- Background: `#FBFAF7`
- Text: `#373532`
- Secondary: `#81796D`
- Accent: `#A7775C`

Characteristics:
- Warm neutrals
- Elegant typography
- Spacious sections
- Restrained accents

### Style C: Technical Minimal

Best for:
- Operations
- Engineering
- Production tracking
- Technical analysis

Colors:
- White: `#FFFFFF`
- Slate: `#344047`
- Light gray: `#F3F5F6`
- Accent: `#74898F`

Characteristics:
- Precise alignment
- Compact data
- Strong readability
- Functional tables

### Style D: Monochrome Finance

Best for:
- Financial models
- Budget planning
- Valuation analysis
- Investment reports

Characteristics:
- Black and white
- Three levels of gray
- Minimal accent colors
- Clear input and formula differentiation
- High-density readable information

### Style E: Subtle Sage

Best for:
- Project trackers
- Planning spreadsheets
- Operational summaries

Characteristics:
- White backgrounds
- Soft green-gray
- Muted sage
- Subtle conditional formatting
- Clear task status indicators

## 9. Design selection

Before generating a workbook:

1. Identify the intended audience.
2. Identify the report purpose.
3. Determine the information density.
4. Select a suitable style.
5. Apply one coherent visual system.
6. Avoid repeating the same design automatically across unrelated reports.

If the user provides a reference image, use it to guide the layout and styling.

If the user provides an undesirable screenshot, treat it as an anti-reference and correct its specific problems.

## 10. Excel functionality

Visual design must never compromise functionality.

Always:

- Preserve existing formulas.
- Preserve source data.
- Maintain references.
- Check calculations.
- Keep editable inputs identifiable.
- Avoid breaking workbook structure.
- Maintain useful worksheet names.
- Use appropriate numeric formats.
- Set freeze panes and print settings when relevant.

For existing workbooks, preserve familiar locations and references unless restructuring is requested.

For financial models, distinguish inputs, calculations, and outputs through subtle, consistent styling.

## 11. Quality-control workflow

### Step 1: Audit

Understand:

- Data structure
- Formulas
- Worksheets
- Existing chart requirements
- Primary information

### Step 2: Plan

Determine:

- Title placement
- Summary placement
- Table position
- Chart position
- Appropriate style

### Step 3: Build

Apply consistent:

- Typography
- Colors
- Spacing
- Borders
- Number formats
- Chart styling

### Step 4: Validate

Check:

- Formula accuracy
- Total calculations
- Data references
- Numeric formats
- Missing or truncated values

### Step 5: Visual inspection

Render or preview the generated Excel workbook whenever possible.

Inspect:

- Column widths
- Row heights
- Table alignment
- Text truncation
- Chart sizes
- Excessive blank space
- Visual balance
- Readability at 100% zoom

Never claim visual verification without actually inspecting the result.

### Step 6: AI-look test

Before delivering, ask:

- Does this look like a default AI spreadsheet?
- Is there too much blue?
- Are there excessive colored blocks?
- Are charts too large?
- Are table headers too dark?
- Are the gridlines distracting?
- Is the design cluttered?
- Can users locate key information quickly?

If any issue exists, improve the design before delivering.

## 12. Prohibited styling

Avoid by default:

- Dark navy table headers
- Large saturated blue bars
- Rainbow color palettes
- Heavy borders around every cell
- Excessive merged cells
- Giant KPI rectangles
- Excessively large charts
- Unnecessary 3D charts
- Bright gradients
- Decorative shadows
- Overly complex dashboards
- Dense technical notes beneath report titles
- Unnecessary charts that duplicate table information

These elements may only be used when explicitly justified by the task.

## 13. Example: Six-month sales report

Preferred layout:

Row 2:
Six-month sales summary

Row 3:
Jan–Jun 2026 | USD | Management overview

Rows 5–7:
Compact revenue, profit and margin KPIs

Row 10:
Light table header

Rows 11–16:
Monthly sales data

Row 17:
Total row with subtle top border

Right side or below:
Small trend chart showing a useful insight

Separate sheet:
Inputs, assumptions and explanatory notes

Use muted colors and maintain a clean first screen.

## 14. Final delivery standards

Deliver a fully editable Excel workbook.

Before completion, verify that:

- The spreadsheet is visually balanced.
- The data is correct.
- Important information is easy to find.
- Colors are restrained.
- Charts are appropriately sized.
- Headers are readable.
- The layout is professional.
- The workbook looks intentionally designed rather than automatically styled.

The final result should feel like a polished management report created by an experienced analyst and designer.

**Core principle: Make Excel look expensive through clarity, not color.**
