# Output schema

Use this schema to produce auditable qualifying-paper results.

## Default final answer structure

For normal scholar runs, the generated Markdown and Word reports contain only:

1. Executive summary
2. Scholar identity and anchors
3. Education and career timeline
4. Qualifying first/co-first/corresponding/co-corresponding journal articles

Keep search coverage, ambiguous candidates, rejection logs, excluded near-matches, caveats, and Evidence Passport details internal unless the user explicitly asks for audit details.

## Academic timeline table

| Period | Institution | Role | Department/Lab | Location | Evidence | Certainty |
|---|---|---|---|---|---|---|

Certainty values:

- `confirmed`
- `inferred`
- `uncertain`
- `unknown`

## Publication table

Default reader-facing table:

| Year | Title | Journal | Scholar role |
|---:|---|---|---|

Internal evidence table fields, retained only in working notes unless requested:

| Field | Meaning |
|---|---|
| `title` | Paper title. |
| `year` | Publication year or best available date. |
| `venue` | Journal, conference, preprint server, or source. |
| `publication_type` | Research article, review, conference/proceedings paper, preprint record, etc. |
| `publication_status` | `published`, `accepted`, `preprint`, `discussion`, `posted_content`, `withdrawn`, or `unknown`. |
| `doi_or_identifier` | DOI, PMID, PMCID, arXiv ID, OpenAlex Work ID, etc. |
| `published_version_doi` | DOI for the final journal/proceedings version when the discovery record was a preprint/discussion/posted-content version. |
| `venue_evidence` | Source used to verify the journal or venue name, such as OpenAlex primary source, Crossref container-title, publisher page, or official CV. |
| `author_list` | Full byline if available. |
| `target_author_printed_name` | Name form printed on article. |
| `target_author_position` | Position in byline. |
| `authorship_role` | Role label. |
| `role_evidence` | Evidence summary for role. |
| `target_printed_affiliation` | Raw affiliation attached to target. |
| `normalized_institution` | Canonical institution. |
| `career_phase_matched` | Timeline phase matched. |
| `identity_evidence` | Evidence supporting target identity. |
| `confidence` | confirmed/strong/moderate/ambiguous/excluded. |
| `source_checked` | Sources used to verify. |
| `notes` | Caveats and special cases. |

## Publication entry format

```text
[Year] Title
Venue: ...
Publication type: ...
Role: co_first + co_corresponding
Role evidence: Equal-contribution footnote linked to target; correspondence line lists target.
Target printed name: ...
Target printed affiliation: ...
Normalized institution: ...
Matched career phase: ...
Identity confidence: confirmed | strong | moderate
Sources checked: ...
Notes: ...
```

Default final-table requirements:

- `venue` must be the verified final journal or accepted/published proceedings venue.
- `publication_status` must be `published` or `accepted` for default final reports.
- `publication_type` must not be standalone `preprint`, `discussion`, `posted-content`, `supplement`, or `withdrawn`.
- Blank venues and placeholders such as `journal pending verification`, `venue pending verification`, `期刊信息待核验`, and `来源待核验` are invalid in default final reports.
- If the discovered version is a preprint/discussion/posted-content record, fill `published_version_doi` after merging to the final article; otherwise exclude it from the default final table unless the user explicitly requested preprints.

## Role labels

Use exactly:

```text
first
co_first
corresponding
co_corresponding
first_and_corresponding
co_first_and_corresponding
co_first_and_co_corresponding
equal_contribution_all_authors
contribution_statement_only
possible_corresponding
role_unconfirmed
```

Default final list includes only:

```text
first
co_first
corresponding
co_corresponding
first_and_corresponding
co_first_and_corresponding
co_first_and_co_corresponding
```

## Identity confidence labels

Use exactly:

```text
confirmed
strong
moderate
ambiguous
excluded
```

## Search coverage summary

Do not add this section to the default Markdown or Word reports. Use it only in internal notes, the concise chat summary when needed, or user-requested audit details.

```text
Searched sources:
- Timeline: official faculty page, ORCID, CV, etc.
- Publication candidates: OpenAlex, Google Scholar, PubMed, dblp, etc.
- Role verification: publisher pages/PDFs, PubMed, Europe PMC, OpenAlex metadata.

Coverage label: high_coverage | medium_coverage | low_coverage
Main limitations: ...
```

## Ambiguous candidates table

Do not add this table to the default Markdown or Word reports. Keep it internally unless requested.

| Year | Title | Reason ambiguous | Evidence needed |
|---:|---|---|---|

Common reasons:

- missing target affiliation;
- same-name collision;
- conflicting author ID;
- role marker unavailable;
- paywalled first page;
- unclear equal-contribution statement.

## Rejection log table

Do not add this table to the default Markdown or Word reports. Keep it internally unless requested.

| Year | Title | Source | Rejection reason | Evidence |
|---:|---|---|---|---|

Allowed reasons:

```text
duplicate
same_name_author
affiliation_mismatch
insufficient_identity_evidence
middle_author_only
role_not_verified
non_paper_item
non_research_item
paywalled_or_unverifiable
publication_version_merged
preprint_without_published_version
venue_unresolved
unresolved_identity_collision
```

## Title-only output

If user asks only for article names, use:

```text
1. [Year] Title
2. [Year] Title
3. [Year] Title
```

Add one concise caveat:

```text
This is a verified title-only list of qualifying papers; role and identity evidence were checked but omitted for brevity.
```

If evidence is incomplete:

```text
This is a provisional title-only list; items marked with * need manual role or identity verification.
```

## Artifact outputs

When the user requests downloadable results, a formal report, or a test run on a scholar, produce report artifacts when the environment supports file creation.

Default artifact behavior:

| Run type | Default artifacts | Notes |
|---|---|---|
| `full` | Markdown + Word. | Preferred for normal scholar reports. |
| `audit` | Markdown + Word. | Keep rejection and contamination findings internal unless requested. |
| `title_only` | Chat summary or Markdown title list. | Evidence is checked but omitted unless requested. |

Recommended filenames:

```text
<scholar-name>-authorship-audit-report.md
<scholar-name>-authorship-audit-report.docx
```

The Word and Markdown reports should contain the same substantive findings. Markdown is better for version control and manual editing; Word is better for sharing, annotation, and institutional reporting.

Do not put only the final titles into the report unless the user explicitly requests title-only output. A full report should include enough evidence for audit.
