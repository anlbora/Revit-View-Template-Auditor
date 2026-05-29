# Revit View Template Auditor

A Dynamo + Python quality control tool for Autodesk Revit that identifies views without assigned view templates and generates severity-based audit reports.

---

# Overview

This project was developed to explore BIM quality assurance workflows using Dynamo, Python, and the Revit API.

The tool automatically scans all project views, identifies views that do not have an assigned view template, classifies issues by severity level, and exports the results to Excel for review.

The objective was to understand how BIM automation can support model governance, documentation standards, and QA/QC processes within Revit projects.

---

# Problem

In large BIM projects, view templates are essential for maintaining consistency across documentation.

Missing view templates may lead to:

* inconsistent graphic standards
* incorrect visibility settings
* coordination issues
* documentation errors
* reduced model quality

Manually checking hundreds of views can be time-consuming and error-prone.

---

# Solution

The View Template Auditor automates the process by:

* collecting all project views
* excluding view templates from the audit
* identifying views without assigned templates
* classifying issues based on view type
* generating a structured audit report
* exporting results to Excel

---

# Features

## View Collection

The script automatically collects:

* Floor Plans
* Sections
* Elevations
* 3D Views
* Schedules
* Other printable views

---

## Missing Template Detection

The workflow checks:

```python
import clr

clr.AddReference("RevitServices")
from RevitServices.Persistence import DocumentManager

clr.AddReference("RevitAPI")
from Autodesk.Revit.DB import *

doc = DocumentManager.Instance.CurrentDBDocument

views = (
    FilteredElementCollector(doc)
    .OfClass(View)
    .ToElements()
)

def get_severity(view_type):
    view_type_text = str(view_type)

    if view_type_text == "ThreeD":
        return "CRITICAL"

    elif view_type_text in ["FloorPlan", "Section", "Elevation"]:
        return "WARNING"

    elif view_type_text == "Schedule":
        return "INFO"

    else:
        return "WARNING"


results = [
    ["View Name", "View Type", "Issue", "Severity"]
]

total_views_checked = 0
issue_count = 0

for view in views:

    if view.IsTemplate:
        continue

    total_views_checked += 1

    if view.ViewTemplateId.IntegerValue == -1:

        issue_count += 1
        severity = get_severity(view.ViewType)

        results.append([
            view.Name,
            str(view.ViewType),
            "NO VIEW TEMPLATE",
            severity
        ])


summary = [
    ["Metric", "Value"],
    ["Total Views Checked", total_views_checked],
    ["Views Without Template", issue_count]
]

OUT = [
    summary,
    results
]
```

and identifies views without assigned templates.

---

## Severity Classification

Issues are categorized automatically:

| View Type  | Severity |
| ---------- | -------- |
| Floor Plan | WARNING  |
| Section    | WARNING  |
| Elevation  | WARNING  |
| 3D View    | CRITICAL |
| Schedule   | INFO     |

---

## Reporting

The tool generates:

### Summary Report

| Metric                 | Value |
| ---------------------- | ----- |
| Total Views Checked    | X     |
| Views Without Template | Y     |

### Issue Report

| View Name       | View Type | Issue            | Severity |
| --------------- | --------- | ---------------- | -------- |
| Level 01        | FloorPlan | NO VIEW TEMPLATE | WARNING  |
| Coordination 3D | ThreeD    | NO VIEW TEMPLATE | CRITICAL |

---

# Workflow

Revit Model
↓
Collect Views
↓
Filter Templates
↓
Check ViewTemplateId
↓
Classify Severity
↓
Generate Report
↓
Export to Excel

---

# Technologies Used

* Autodesk Revit
* Dynamo
* Python
* Revit API
* Excel Integration

---

# Skills Demonstrated

* BIM QA/QC automation
* Revit API integration
* Model auditing workflows
* Data filtering
* Severity classification systems
* Excel reporting
* BIM standards validation
* Automation-driven quality control

---

# Future Improvements

Planned future improvements include:

* configurable severity rules
* exclusion of specific view types
* project-specific BIM standards
* automatic template assignment
* dashboard visualization
* Power BI integration
* BIM execution plan compliance checks

---

# Learning Notes

This project was developed as part of a BIM automation learning journey focused on practical quality control workflows.

The exercise demonstrates how relatively simple Revit API scripts can automate repetitive auditing tasks and support more consistent BIM management processes.
