# Artifact output rules

Use these rules when the user wants a reusable report, a test run of the skill, or downloadable results.

## Default behavior

For a normal full run on a scholar, produce only:

1. a concise chat summary;
2. a Markdown report (`.md`);
3. a Word report (`.docx`) when document generation is available.

Do not create CSV, XLSX, Evidence Passport JSON, rejection-log files, companion files, or raw-evidence deliverables by default. Keep evidence and rejection details internal unless the user explicitly asks for audit files.

Do not create or leave `tmp`, `cache`, raw-response, checkpoint, payload, parsed-evidence, downloaded-HTML, or other intermediate files anywhere under the final output root during a normal run. If temporary files are needed for retrieval, use an OS/session temporary location outside `outputs/scholar-authorship-auditor/` and delete them before returning.

If the user explicitly requests only one format or asks for extra audit files, follow that request.

## Report language

Use the same language as the user's request for the report body. For Chinese requests, generate Chinese reports. For English requests, generate English reports. Preserve paper titles, journal names, author names, identifiers, and quoted source text in their original form unless translation is explicitly requested.

## Recommended filenames

```text
<scholar-name>-authorship-audit-report.md
<scholar-name>-authorship-audit-report.docx
```

## Output location

If the user provides an output directory or explicit file paths, use those paths.

If the user does not specify a location, write the two report files under the current working directory:

```text
outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/
```

Do not write run outputs into the skill installation directory under `.codex/skills`. That directory is for the skill's reusable instructions, scripts, references, and templates.

The default output root must not contain sibling helper directories such as:

```text
outputs/scholar-authorship-auditor/tmp/
outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/tmp/
```

Only create those directories when the user explicitly requests audit files or raw evidence.

## Fixed Word template

Use `assets/authorship-audit-template.docx` as the default Word report template.

Prefer `scripts/render_report.py` after the final screened article list is ready. It renders both Markdown and DOCX from a small JSON payload:

```bash
python scripts/render_report.py payload.json
```

Payload fields:

- `report_title`
- `executive_summary`
- `identity_and_anchors`
- `timeline`
- `articles`
- `output_md` (optional; defaults to the standard output directory)
- `output_docx` (optional; defaults to the standard output directory)
- `language`

Workflow:

1. Copy the template to the final `.docx` path.
2. Replace text placeholders:
   - `{{REPORT_TITLE}}`
   - `{{EXECUTIVE_SUMMARY}}`
   - `{{IDENTITY_AND_ANCHORS}}`
3. Populate the first table after the education/career heading with timeline rows.
4. Populate the second table after the qualifying-articles heading with the final article rows.
5. Preserve the template section order and table columns.
6. Validate the generated `.docx` before returning it.
7. Delete or avoid persisting the JSON payload unless the user explicitly requested audit files.

If a payload file is necessary to run the helper, create it in a temporary directory outside the final output root, run the helper, then delete it. Do not leave `<scholar-name>-authorship-payload.json` beside the reports by default.

If the template cannot be opened, recreate the same structure with `python-docx` and run validation. Do not redesign the report layout during normal runs.

## Report sections

Use only these sections by default:

1. Executive summary
2. Scholar identity and anchors
3. Education and career timeline
4. Qualifying first/co-first/corresponding/co-corresponding journal articles

Do not add default sections for search coverage, limitations, notes, preprint candidates, ambiguous candidates, rejection logs, evidence tables, Evidence Passport, companion files, or supporting files.

## Executive summary

The executive summary must include:

1. the number of qualifying journal articles in the final table;
2. whether the scholar has published a qualifying article in the main journals `Nature` or `Science`;
3. if yes, the title(s), year(s), journal(s), and scholar role(s) for those main-journal articles;
4. if no, a concise statement that no qualifying main-journal `Nature` or `Science` article was identified under the fixed authorship rule.

For this item, count only exact main-journal `Nature` and exact main-journal `Science`. Do not count Nature-family journals such as `Nature Geoscience`, `Nature Climate Change`, or `Nature Sustainability`; do not count Science-family journals such as `Science Advances`, `Science Bulletin`, or `Science of the Total Environment`.

## Scholar identity and anchors

This section should include names and identifiers plus a concrete curriculum-style biography. Do not only list ORCID, homepage, or current affiliation.

Include every verified education and career stage found from official/ORCID/CV evidence, especially:

- undergraduate degree;
- master's degree if any;
- Ph.D. training;
- postdoctoral positions;
- assistant, associate, or full professor appointments;
- PI/faculty appointments;
- academy titles when applicable;
- current affiliation.

For each stage, include dates, institution, department/lab, role/title, and evidence certainty when available. Do not invent unknown stages.

## Article table

The final article table should contain only reader-facing columns:

```text
Year | Title | Journal | Scholar role
```

The `Journal` column must contain only the journal or venue name. Do not include volume, issue, page range, article number, DOI, URL, database name, or source-system labels in that column. Do not include DOI or source links in the default table unless the user explicitly asks for identifiers or links.

Standalone preprints, discussion papers, and posted-content records are internal discovery evidence by default. Merge them into the published article when a journal version exists. If no final journal/proceedings version exists, exclude them from the default Markdown and Word reports unless the user explicitly asks for provisional preprints.

## Consistency rule

The Markdown and Word reports should contain the same substantive findings and the same final article table.

## Caveat rule

Do not claim that the list is exhaustive unless the quality gates and coverage requirements support that claim. If coverage is incomplete, keep the uncertainty in the concise chat summary rather than adding a separate limitations section to the generated report, unless the user explicitly asks for methodology details.

## Final output audit

Before the final chat answer, list the final run directory and the `outputs/scholar-authorship-auditor/` root. For a normal run, only the Markdown report and Word report should remain in the run directory, and no `tmp` sibling directory should remain. Remove any default-created intermediate files before linking the reports.
