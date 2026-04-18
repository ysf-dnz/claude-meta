---
name: ads-report
description: "PDF audit report generation for client deliverables. Converts audit markdown output into a professional PDF with health score gauge, platform bar charts, pass/fail distribution, formatted tables, and zero overlap. Requires reportlab and matplotlib. Use when user says generate report, PDF report, client report, export audit, or ads report."
user-invokable: false
---

<!-- Updated: 2026-04-18 | v1.0 -->

# Ads Report: PDF Audit Report Generation

## Process

1. Confirm audit results are available (from `/ads audit` or a platform-specific audit)
2. Save audit output to a markdown file if not already on disk (e.g. `audit-results.md`)
3. Run quality gate check before generating the final PDF
4. Generate the PDF report
5. Confirm output path and summary to the user

## Commands

### Step 1 — Quality Gate (always run first)

```bash
python scripts/generate_report.py audit-results.md --check
```

Fix any warnings before proceeding. Do not skip this step.

### Step 2 — Generate PDF

```bash
python scripts/generate_report.py audit-results.md --output report.pdf --brand "Client Name"
```

| Flag | Description |
|------|-------------|
| `--output` / `-o` | Output path (default: `audit-results.pdf` next to input file) |
| `--brand` / `-b` | Client/brand name shown in report header |
| `--check` | Validate layout without writing the PDF |
| `--json` | Emit machine-readable summary JSON alongside PDF |
| `--verbose` / `-v` | Show detailed generation logs |

## Quality Gates

Reports must pass all of the following before delivery:

- No overlapping elements
- Proper margins (0.75 in)
- Word-wrapped table cells (no clipping)
- All charts and images sized within page boundaries
- Page numbers and section dividers present
- Captions on every visual
- Zero empty sections

If `--check` returns warnings, fix the underlying audit markdown (remove malformed tables, empty sections, overly long lines) and re-run.

## Dependencies

```bash
pip install reportlab matplotlib
```

Both are listed in `requirements.txt`. If missing, prompt the user to install before proceeding.

## Output

- `report.pdf`: Client-ready PDF with:
  - Cover page (brand name, date, overall Ads Health Score)
  - Health score gauge chart
  - Per-platform bar chart (scores 0–100)
  - Pass/fail distribution pie chart
  - Findings table (priority, check ID, description, recommendation)
  - Quick wins section
  - Action plan with priority tiers (Critical → High → Medium → Low)

## Error Handling

| Error | Fix |
|-------|-----|
| `reportlab required` | `pip install reportlab` |
| `matplotlib required` | `pip install matplotlib` (optional — charts render as text fallback) |
| `--check` warnings | Clean audit markdown; remove empty sections or malformed tables |
| Input file not found | Re-run audit and save output to a `.md` file first |
