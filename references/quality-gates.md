# Quality gates

Use these gates before producing a final qualifying-paper list. A failed gate does not always stop the run, but it lowers confidence and must be disclosed.

## Gate summary

| Gate | Purpose | Pass condition | Failure handling |
|---|---|---|---|
| Identity Anchor Gate | Ensure the target can be distinguished. | Name + at least one identity anchor. | Ask for anchor or proceed as low coverage. |
| Timeline Gate | Build disambiguation spine. | At least one dated or strongly evidenced education/employment phase. | Use low-confidence mode; avoid exhaustive claims. |
| Candidate Coverage Gate | Avoid single-source bias. | At least two independent sources checked; field source when applicable. | State coverage limitation. |
| Publication Existence Gate | Prevent fabricated or hallucinated titles. | DOI/PMID/arXiv/OpenAlex/Semantic Scholar/Crossref/publisher/official CV source. | Exclude from confirmed list. |
| Published Version and Venue Gate | Prevent preprints or unresolved venues from entering the journal-article table. | Final record is a journal article or accepted/published proceedings paper with a verified venue. | Merge to published version or exclude from default final table. |
| Institution Normalization Gate | Avoid naive string matching. | Target author affiliation parsed and normalized, or alternative identity evidence recorded. | Lower confidence or mark ambiguous. |
| Identity Match Gate | Separate same-name authors. | Confirmed/strong/moderate identity evidence. | Move to ambiguous/excluded. |
| Authorship Role Gate | Verify first/co-first/corresponding status. | Role evidence from byline, footnote, publisher/PDF/JATS, or reliable metadata. | Exclude from confirmed list or mark provisional. |
| Deduplication Gate | Avoid duplicate counting. | Versions merged by DOI/ID/title/venue evidence. | Flag possible duplicates. |
| Output Audit Gate | Ensure the concise deliverables are internally checked. | Final Markdown and Word reports include the qualifying article table only, and internal notes retain enough evidence to explain decisions if asked. | Fix the report or add a concise chat caveat; do not add default rejection-log or coverage sections to the report. |

## Gate details

### 1. Identity Anchor Gate

Pass if at least one anchor exists beyond the name:

- current/past institution;
- field;
- ORCID;
- official profile/CV;
- Google Scholar profile;
- representative paper;
- native-script name + institution;
- advisor/lab.

If only a common name is supplied, ask for one anchor. If forced to proceed, label `low_coverage`.

### 2. Timeline Gate

Pass if the timeline contains at least one strong dated institution or role. Prefer full education-to-appointment reconstruction, but do not invent missing stages.

### 3. Candidate Coverage Gate

For a full run, pass if at least:

- one official/profile source and one bibliographic database are checked; or
- two independent bibliographic databases are checked plus timeline evidence;
- field-specific source is checked when field demands it.

### 4. Publication Existence Gate

A title should not enter the final list unless it can be traced to a source.

Strong identifiers:

- DOI;
- PMID/PMCID;
- arXiv/bioRxiv/ChemRxiv ID;
- OpenAlex Work ID;
- Semantic Scholar Paper ID;
- publisher page;
- official CV/publication page.

### 5. Published Version and Venue Gate

Pass if each final table record has:

- a final publication status such as `journal-article`, accepted conference/proceedings paper, or equivalent formally published paper-like record;
- a verified journal or venue name;
- no unresolved preprint/discussion/posted-content version when a published version should be checked.

Fail if:

- the only located record is a standalone preprint, discussion paper, posted-content record, supplement, withdrawn manuscript, or a page marked not accepted for further review;
- the journal/venue is blank;
- the journal/venue is a placeholder such as `journal pending verification`, `venue pending verification`, `期刊信息待核验`, or `来源待核验`;
- a preprint/discussion DOI has not been checked against Crossref/OpenAlex title search for a final version.

On failure, search for and merge to the published version. If no final version is found, exclude the record from the default final journal-article table unless the user explicitly requested provisional preprints.

### 6. Institution Normalization Gate

Pass if the target author's own affiliation is parsed and mapped to timeline or a strong alternative identity marker exists.

Do not pass using coauthor affiliation.

### 7. Identity Match Gate

Pass if identity confidence is `confirmed`, `strong`, or acceptable `moderate`.

For normal full runs, `confirmed`, `strong`, and acceptable `moderate` identity evidence can pass when role evidence is adequate and no same-name conflict remains.

### 8. Authorship Role Gate

Pass if the target is verified as first, co-first, corresponding, or co-corresponding.

Do not pass based only on last-author position.

### 9. Deduplication Gate

Pass when duplicates/preprints/published versions have been merged or explicitly marked.

### 10. Output Audit Gate

Pass if final output includes:

- timeline;
- included qualifying papers;
- no default sections for search coverage, limitations, notes, preprint candidates, ambiguous candidates, rejection logs, evidence tables, Evidence Passport, companion files, or supporting files;
- no default CSV/XLSX/JSON/rejection-log deliverables;
- a final article table with only year, title, journal, and scholar role columns;
- no blank or placeholder journal values;
- no standalone preprints, discussion papers, posted-content records, supplements, withdrawn manuscripts, or records marked not accepted for further review in the default final table.

Internal evidence, rejected candidates, coverage labels, and caveats should still be retained in working notes when useful, but they should not be exported or inserted into the generated Markdown/Word reports unless the user explicitly asks for audit details.

## Confidence labels

| Label | Use |
|---|---|
| `confirmed` | Official source or decisive identifier verifies identity and role. |
| `strong` | Timeline-affiliation match and role evidence are strong, no conflict. |
| `moderate` | Likely identity or role, but one key field is incomplete. |
| `ambiguous` | Same-name collision or insufficient evidence. |
| `excluded` | Fails identity, role, or paper-type requirements. |
