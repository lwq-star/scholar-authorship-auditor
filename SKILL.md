---
name: scholar-authorship-auditor
description: >-
  Reconstruct a scholar's education and career timeline, then search, verify,
  and audit papers where the target scholar is first author, co-first author,
  corresponding author, or co-corresponding author. Use this skill when a user
  asks for all papers, all articles, all publications, representative papers,
  scholar-authorship audits, first-author/co-first-author/corresponding-author
  paper lists, publication-list cleanup, author disambiguation, or downloadable
  Word or Markdown reports about a scholar's qualifying papers. The skill
  instructions are English-only; final answers and generated reports should
  match the user's language.
---

# Scholar Authorship Auditor

## Skill metadata

- Version: 0.7.5
- Status: active
- Data access level: raw-to-verified
- Task type: open-ended evidence audit
- Default run type: `full`
- Fixed target output: papers where the target scholar is `first`, `co_first`, `corresponding`, or `co_corresponding`.
- Default artifact output for a full/test run: Markdown report (`.md`) + Word report (`.docx`) only, when file creation is available. Do not leave `tmp`, cache, raw-response, checkpoint, payload, or audit sidecar files in the final output tree unless the user explicitly requests audit files.
- Default retrieval spine: sequential OpenAlex batch author-works enumeration first; webpage/PDF checks only for ambiguous or high-stakes boundary cases.

## Language policy

The skill files and internal instructions must remain English-only.

When producing a chat answer or generated report:

- if the user's request is in Chinese, write the response and reports in Chinese;
- if the user's request is in English, write the response and reports in English;
- if the request is mixed-language, use the dominant language of the user's latest request;
- preserve paper titles, venue names, author names, identifiers, and quoted source text in their original form;
- translate institution names only when a reliable official translation exists or the user asks for translation.

## Fixed operational policy

This skill has one fixed goal:

> Find papers where the target scholar is first author, co-first author, corresponding author, or co-corresponding author.

When the user asks for "all papers", "all articles", "all publications", "all works", "all published articles", or the equivalent in another language, interpret that as **all qualifying papers under this fixed authorship rule**. In this skill, only papers where the target scholar is first author, co-first author, corresponding author, or co-corresponding author count as the scholar's papers. Other coauthored papers are candidate or rejected records unless the user explicitly asks for a broader coauthorship corpus.

Do not ask the user to choose a publication range. Publication-range options are intentionally not part of this skill.

The search can use broad bibliographic sources to avoid missing candidates, but the final answer must include only qualifying papers where the target scholar's identity and authorship role are supported by evidence.

## What counts as a qualifying paper

Include a record only if all three conditions are met:

1. It is a paper-like scholarly record with a byline and identifiable authorship metadata, such as a research article, review article, conference/proceedings paper, or accepted/published paper record.
2. The record belongs to the target scholar after identity disambiguation.
3. The target scholar is verified as one or more of:
   - `first`;
   - `co_first`;
   - `corresponding`;
   - `co_corresponding`;
   - `first_and_corresponding`;
   - `co_first_and_corresponding`;
   - `co_first_and_co_corresponding`.

Do not include by default:

- middle-author-only papers;
- last-author papers without explicit corresponding-author evidence;
- generic CRediT contribution statements without co-first or correspondence evidence;
- equal-contribution statements that apply to all authors but do not identify a co-first author group;
- patents, datasets, theses, news items, editorials, corrections, errata, posters, collaborator appendices, or consortium-only records;
- records with unresolved same-name collision;
- records whose role cannot be verified.

Standalone preprints, discussion papers, and posted-content records are used only as discovery or version evidence in the default journal-article workflow. If a published article or conference version exists, merge the preprint into that published version instead of counting it separately. If no published version exists, exclude the record from the default final journal-article table unless the user explicitly asks to include provisional preprints.

## Purpose

This skill finds and verifies a target scholar's qualifying papers. The central method is:

1. reconstruct the scholar's education and employment timeline;
2. enumerate name variants and identity anchors;
3. collect candidate papers broadly from reliable sources;
4. deduplicate versions;
5. normalize institutions and match paper affiliations to the timeline;
6. verify first/co-first/corresponding/co-corresponding role from article-level evidence;
7. produce a concise chat summary plus Markdown and Word reports for full/test runs.

Never start from a name-only paper search when a timeline can be reconstructed. A name is not an identifier. Career timeline, institution history, identifiers, coauthor network, advisor/lab, topic continuity, and author-role evidence are all required to separate the target from same-name authors.

## When to use this skill

Use this skill when the user asks for any of the following:

- all papers, all articles, all publications, all published articles, all works, or equivalent wording for a scholar;
- a scholar's first-author papers;
- a scholar's co-first-author papers;
- a scholar's corresponding-author papers;
- a scholar's co-corresponding-author papers;
- academic CV reconstruction for paper attribution;
- publication list verification or cleanup;
- Google Scholar, ORCID, OpenAlex, PubMed, dblp, CNKI, CV, or homepage audit;
- author disambiguation for a common name;
- Word or Markdown report about a scholar's qualifying papers.

Trigger even if the user provides only a name plus field/institution. If the name is common and no identity anchor is provided, ask for one anchor before attempting an exhaustive search.

## Required inputs

Ask only for missing identity information. Minimum viable input:

1. full scholar name;
2. at least one identity anchor:
   - current or past institution;
   - research field;
   - ORCID;
   - Google Scholar profile;
   - official homepage or CV;
   - representative paper;
   - native-script name such as Chinese, Japanese, or Korean;
   - advisor, lab, or recurring collaborator.

Do not ask the user to choose publication range or author-role rules. The author-role rule is fixed: first/co-first/corresponding/co-corresponding only.

If the user says "all papers" without further role wording, do not create a separate all-coauthored-paper deliverable by default. Treat the request as the fixed qualifying-paper audit and optionally mention that broader middle-author coauthorship lists are outside the default definition.

If the user asks for a test run, a formal report, or downloadable output, generate both a Markdown report and a Word report unless the environment cannot create files or the user explicitly asks for only one format.

## Internal run types

Run types are internal execution choices, not user-facing publication-range options or alternate speed modes. For normal scholar requests, use `full`; use the other labels only when the user's request clearly matches them.

| Run type | When to use | Behavior |
|---|---|---|
| `full` | Default. | Timeline + broad candidate retrieval + role verification + Markdown and Word reports. |
| `audit` | User provides CV, Google Scholar list, spreadsheet, or manually compiled list. | Corpus-first screening, then external search to fill gaps and detect contamination. |
| `title_only` | User explicitly asks only for paper names. | Verify internally; output concise title list plus caveat. |
| `resume` | Continue a prior run. | Load and update the Evidence Passport or prior output if available. |

Default is `full`.

Do not introduce separate user-facing fast-versus-strict modes for speed. The default `full` run uses one sequential workflow: OpenAlex batch first, local filtering second, and webpage/PDF verification only for ambiguous cases.

## Evidence standard

Every final included paper must have three evidence dimensions:

1. **Existence evidence**: DOI, PMID, PMCID, arXiv ID, OpenAlex Work ID, Semantic Scholar Paper ID, Crossref record, publisher page, official CV entry, or equivalent.
2. **Identity evidence**: timeline-affiliation match, ORCID, author ID, official CV, coauthor/lab/topic continuity, email domain, advisor network, or institution evidence.
3. **Role evidence**: byline order, equal-contribution footnote, correspondence line, author footnote, target email in correspondence block, publisher/JATS/PDF metadata, or reliable database role metadata.

If any evidence dimension is incomplete, label the record `moderate`, `ambiguous`, or `provisional`. Do not silently include it as confirmed.

## Workflow

### Phase 0 — Initialize the Evidence Passport

Before searching, create an Evidence Passport. Record:

- skill version;
- run date;
- user inputs;
- internal run type;
- data sources planned;
- identity anchors available;
- known constraints;
- intended artifact outputs.

See `references/evidence-passport.md`.

### Phase 1 — Identity Anchor Gate

Verify that at least one identity anchor exists. A name alone is not enough for exhaustive search.

Pass conditions:

- full name + current/past institution; or
- full name + ORCID; or
- full name + official homepage/CV; or
- full name + field + representative paper; or
- full name + native-script name + institution/field.

If this gate fails, ask for one anchor. If proceeding anyway, label the run `low_coverage` and do not claim completeness.

### Phase 2 — Build the scholar identity profile and timeline

Reconstruct education and employment from undergraduate study to current appointment when public evidence exists.

Collect:

- native-script name;
- romanization variants;
- initials;
- name-order variants;
- maiden/married/changed names;
- ORCID;
- Google Scholar profile;
- institutional page;
- CV or publication list;
- email domain;
- advisor;
- lab, department, hospital, or research group;
- recurring coauthors;
- core research topics;
- institution aliases and IDs.

Timeline table fields:

| Period | Institution | Role | Department/Lab | Location | Evidence source | Certainty |
|---|---|---|---|---|---|---|

Rules:

- Prefer official, dated sources.
- Mark inferred dates as inferred.
- Do not invent unknown education history.
- Record uncertain or missing stages explicitly.
- Capture transitions carefully; most disambiguation errors happen around moves.
- In the generated "Scholar identity and anchors" section, write a concrete curriculum-style biography rather than only listing identifiers. Include every verified education and career stage found from official/ORCID/CV evidence, especially undergraduate degree, master's degree if any, Ph.D. training, postdoctoral positions, assistant/associate/full professor appointments, academy titles, and current affiliation. Use dates, institution names, department/lab, role/title, and evidence certainty when available.
- If a stage is not publicly verified, state it as unknown internally; do not invent it and do not add a vague placeholder to the final report.

### Phase 3 — Corpus-first screening if user provides a corpus

If the user provides a CV, ORCID, Google Scholar page, official publication list, spreadsheet, Zotero/BibTeX file, PDF set, or manually compiled list:

1. screen the provided corpus first;
2. apply the same inclusion/exclusion rules as external search;
3. log every excluded item with a reason;
4. do not treat self-curated profiles as automatically correct;
5. use external databases to fill gaps and detect contamination.

No silent skipping is allowed.

### Phase 4 — Candidate paper retrieval

Use a single sequential OpenAlex-batch-first workflow. Search other sources to identify the scholar and fill OpenAlex gaps, not as a default per-paper webpage crawl.

#### Source preflight and transient-failure handling

Run a source preflight before bulk retrieval:

- Preflight only sources planned for the current sequential path. Always preflight OpenAlex before batch enumeration and one identity/profile anchor source such as an official page, ORCID, CV, or Google Scholar profile. Do not preflight Semantic Scholar, PubMed, Crossref, field databases, or publisher sites unless they are actually needed for a gap or ambiguous case.
- Record status code, endpoint, timestamp, response content type, rate-limit or quota fields, and error class in the Evidence Passport.
- If a required preflight fails, retry before downgrading coverage. Do not spend retries on optional gap sources before the OpenAlex batch result shows they are needed.

Retry transient network failures:

- Treat `SSLError`, `ConnectionError`, `ReadTimeout`, `ChunkedEncodingError`, HTTP `408`, `425`, `429`, `500`, `502`, `503`, and `504` as retryable unless the source documentation says otherwise.
- Use exponential backoff with jitter for OpenAlex bulk enumeration and the one critical identity/profile source, with up to three attempts for those required sources.
- For optional gap sources such as Crossref, Semantic Scholar, PubMed, local-language databases, or publisher pages, use at most one retry before skipping, downgrading, or adding the item to the ambiguous-case queue.
- If Python `requests` fails with a TLS or SSL EOF error on a required source, try one alternate retrieval path such as `curl`, PowerShell `Invoke-WebRequest`, a browser fetch, or a narrower endpoint before declaring the source unavailable. Do not run alternate retrieval paths for optional sources unless they affect an included or ambiguous final record.
- Save the final error text and every attempted endpoint, but do not let one failed source silently remove the scholar from downstream candidate retrieval.

Preserve internal raw evidence without leaking artifacts:

- If full raw responses or slim audit tables are needed during analysis, keep them in memory or in an OS/session temporary location outside the final output tree.
- Preserve enough raw metadata to re-check author order, affiliations, correspondence flags, source IDs, and publication versions.
- Do not add persistent cache files for this skill by default.
- Do not create or leave a `tmp/`, `cache/`, `raw/`, `evidence/`, `checkpoint/`, or similar intermediate folder under `outputs/scholar-authorship-auditor/` or under a run output directory unless the user explicitly asks for audit files.
- If temporary files are unavoidable for a long retrieval, delete them before the final response or move them only when the user explicitly requests audit files.
- Never store API keys or bearer tokens in raw-response files, logs, reports, or internal evidence records.

Sequential speed discipline:

- Do not query a source merely because it exists in the source stack. Query it only when it answers a current identity, recall-gap, metadata-normalization, or role-ambiguity question.
- After OpenAlex batch enumeration, local role filtering, deduplication, and one independent recall-gap check, stop broad retrieval if there are no missing titles, same-name conflicts, DOI/version conflicts, or ambiguous role cases.
- Do not run Crossref, Semantic Scholar, PubMed, field-specific databases, local-language databases, publisher pages, or PDFs just to increase source count when OpenAlex and the recall-gap check already support the final list.

#### OpenAlex quota and API-key handling

When using OpenAlex:

- Prefer authenticated requests when an OpenAlex API key is available from an environment variable or secret manager.
- Never hard-code, store, echo, log, or copy a user's OpenAlex API key into this skill file, scripts, reports, caches, logs, internal evidence records, or chat summaries.
- If OpenAlex returns a quota, budget, rate-limit, `429`, or `Insufficient budget` error, tell the user that the OpenAlex free quota appears to be exhausted before retrying large OpenAlex-dependent enumeration.
- In that message, recommend the user spend about 30 seconds creating a free OpenAlex account to obtain a free API key, then provide it through an environment variable or other secret-safe channel.
- Do not imply that payment is the only way to continue. Paid access is only one option for needs beyond the free authenticated quota.
- If no fresh quota or API key is available, continue with alternate sources such as ORCID, Semantic Scholar, Crossref, PubMed, publisher pages, official CVs, and local-language databases, but downgrade the OpenAlex coverage caveat accordingly.

OpenAlex batch enumeration workflow:

- Resolve the target OpenAlex author ID from ORCID, official homepage/CV, Google Scholar-linked titles, current/past institutions, and topic/coauthor fingerprints.
- Prefer full author-works enumeration with an authenticated API key: use the author works endpoint or an equivalent works filter, paginate sequentially with the largest stable page size allowed by the API, and request only fields needed for local screening.
- Use OpenAlex `select` or equivalent field selection when available. Keep fields such as `id`, `doi`, `title`, `publication_year`, `publication_date`, `type`, `authorships`, `primary_location`, `locations`, and `ids`; avoid abstracts, concepts, references, citation lists, and per-work detail calls unless a record enters the ambiguous-case queue.
- Treat OpenAlex `authorships.author_position=first` for the target authorship as first-author evidence when identity is strong.
- Treat OpenAlex `authorships.is_corresponding=true` for the target authorship as corresponding-author evidence when identity is strong.
- Build the initial qualifying list locally from OpenAlex authorships before opening publisher pages.
- Build an ambiguous-case queue for webpage/PDF verification. Include only: possible co-first/equal-contribution records, missing or conflicting correspondence metadata, missing or blank venue/source metadata, title/DOI/version conflicts, preprint/discussion-paper/posted-content records that may have a later journal version, records discovered outside OpenAlex, high-stakes `Nature` or `Science` main-journal records, same-name collisions, and cases where official CV/homepage role notation conflicts with OpenAlex.
- Do not open publisher pages or PDFs for every OpenAlex first-author or `is_corresponding=true` record. Use publisher pages/PDFs only for the ambiguous-case queue.
- If author-works enumeration is quota-limited, use DOI-level OpenAlex work lookups only for high-priority candidates and clearly label the run as not fully OpenAlex-enumerated.

ORCID optimization:

- Try both record-level and works-level public endpoints when appropriate.
- If ORCID public API fails transiently, retry and then use the public ORCID web profile as a fallback identity anchor.
- Treat ORCID as strong identity evidence when it links to the work or author, but do not assume ORCID contains a complete publication list.

Semantic Scholar optimization:

- Use Semantic Scholar as a high-recall candidate source, not as the sole authority for identity or role.
- Export a broad Semantic Scholar candidate table only when it helps audit recall; label it as candidate data, not the final qualifying-paper list.
- Reconcile Semantic Scholar records with DOI, OpenAlex, Crossref, publisher pages, and official CV entries before final inclusion.

Recommended source order:

1. identity/timeline sources: official CV/homepage, ORCID, Google Scholar profile, local-language institutional pages;
2. OpenAlex author resolution and full OpenAlex author-works enumeration;
3. official CV/homepage, ORCID works, and Google Scholar profile as lightweight recall-gap checks against the OpenAlex batch list;
4. Crossref DOI/title metadata only for records missing DOI, venue, publication date, publication type, or version clarity after OpenAlex;
5. Semantic Scholar, field-specific databases, and local-language databases only for candidates missing from OpenAlex or unresolved by the first four steps;
6. publisher pages and article PDFs only for ambiguous-case role verification.

For OpenAlex batch candidates, collect from the batch metadata without opening pages:

- title;
- year and best available publication date;
- venue;
- DOI or identifier;
- full author list;
- target printed name;
- target author position;
- target printed affiliation;
- corresponding/equal-contribution metadata if available;
- source URL or database;
- author IDs;
- publication type.

For records outside the OpenAlex batch or records in the ambiguous-case queue, collect the same fields from the narrowest source that resolves the uncertainty. Do not fetch full pages or PDFs when DOI, title, venue, author role, and identity are already sufficient.

Published-version and venue resolution:

- Treat OpenAlex `type=preprint`, Crossref `type=posted-content`, arXiv/bioRxiv/medRxiv/ChemRxiv/SSRN/Research Square records, and publisher labels such as `preprint`, `discussion paper`, `Discuss.`, `submitted`, or `not accepted` as non-final by default.
- When a candidate is non-final, has a blank venue/source, or has an uncertain venue, search Crossref and OpenAlex by normalized title and DOI family before final inclusion.
- If a journal-article version exists with the same DOI family, title similarity >= 0.90, or a publisher page explicitly linking the discussion/preprint version to the final article, merge into the journal-article version and use the final journal venue, year/date, DOI, and publication type.
- If no journal-article or accepted conference/proceedings version can be found, keep the record only as internal discovery evidence. Do not place it in the default final journal-article table.

### Phase 5 — Deduplicate and verify publication existence

Deduplicate by:

- DOI;
- PMID/PMCID;
- arXiv/bioRxiv/ChemRxiv identifier;
- OpenAlex Work ID;
- Semantic Scholar Paper ID;
- normalized title;
- venue + year + page/article number;
- publisher landing page when already available or needed for an ambiguous duplicate.

Title matching rules:

- exact DOI/PMID/arXiv match: merge automatically;
- normalized title similarity >= 0.90: strong duplicate candidate;
- 0.75-0.90: possible duplicate, manual review;
- below 0.75: do not merge automatically.

If a preprint and published version are the same work, keep the published version as primary and record the preprint as an alternate version. If author order or role differs between versions, flag the discrepancy.

Default final-list publication status rules:

- Include only `journal-article`, accepted/published conference/proceedings papers, or equivalent formally published paper-like records.
- Exclude standalone preprints, discussion papers, posted-content records, supplements, withdrawn manuscripts, and records whose review page says the manuscript was not accepted for further review.
- Do not use a blank venue or a placeholder such as `journal pending verification`, `venue pending verification`, `期刊信息待核验`, or `来源待核验` in the final report. Resolve the venue before final inclusion, merge to the published version, or exclude the record from the default final table.

For each merge candidate, compare DOI, normalized title, year, venue, first author, full or partial author list, and arXiv/preprint identifier. Use a publisher landing page only when these local identifiers are insufficient. Do not let a subtitle, punctuation, transliteration difference, or database-created duplicate create two final records for the same scholarly work. Conversely, do not merge distinct articles only because they share a short or generic title.

### Phase 6 — Institution normalization

Normalize affiliations before matching them to the timeline.

Rules:

- Use the target author's own affiliation, not a coauthor's affiliation.
- Preserve raw affiliation strings.
- Extract department, lab, institute, hospital, parent university, city, and country.
- Map to canonical institution entities when possible: ROR ID, OpenAlex institution ID, official English/local-language name, abbreviation, alias, historical name.
- Use institution IDs, display names, and raw affiliation strings already present in OpenAlex authorships first. Do not run separate ROR or institution-lookup searches for every record unless identity depends on resolving an ambiguous affiliation.
- Treat affiliated hospitals, umbrella research organizations, national academies, and university systems carefully; match at the most specific level available.
- Department-level mismatch under the same parent institution is not usually fatal, but should lower confidence if field or lab does not match.

See `references/institution-normalization.md`.

### Phase 7 — Identity disambiguation

For each candidate, classify identity confidence.

Once a stable target OpenAlex Author ID is resolved, use it as the identity spine for OpenAlex batch records. Re-audit identity record-by-record only when an authorship affiliation, topic, coauthor network, date, or external profile check conflicts with the target timeline.

Use the best available paper date in this order:

1. submitted/received date;
2. accepted date;
3. online-first or early-access date;
4. final issue publication date;
5. publication year only.

Time-affiliation rules:

- `confirmed`: official CV/ORCID/publisher/PDF/identifier explicitly ties the target to the paper and role.
- `strong`: target affiliation matches a timeline institution active at the best paper date, or within 1 year after departure with topic/coauthor continuity.
- `moderate`: target affiliation matches a former timeline institution within 2 years after departure, or a future institution within 1 year before start with evidence of visiting/joint/early appointment, or affiliation is missing but at least two secondary fingerprints match.
- `ambiguous`: overlapping same-name authors, missing affiliation, conflicting author IDs, or insufficient secondary evidence.
- `excluded`: affiliation, field, author ID, location, or era clearly belongs to another person.

The 2-year lag window is a recall and moderation window, not an automatic strong-match rule. A +2 year old-affiliation match requires corroborating evidence.

Secondary fingerprints:

- ORCID;
- author ID;
- email handle/domain;
- advisor or lab head;
- recurring coauthors;
- topic continuity;
- grants/projects;
- methods/datasets;
- institution city/country;
- acknowledgment/lab affiliation.

### Phase 8 — Authorship role verification

Filter only after identity is matched.

Keep papers where the target is:

- first author;
- co-first author;
- corresponding author;
- co-corresponding author.

Do not infer corresponding author from last-author position. Last author is only a prompt to verify.

Detailed rules are in `references/authorship-detection.md`.

Use evidence tiers for role verification:

| Tier | Evidence | Use in final list |
|---|---|---|
| `publisher_confirmed` | Publisher HTML/PDF/JATS byline, equal-contribution note, correspondence block, target email, or official article page explicitly identifies the role. | Strongest evidence; prefer for final role labels. |
| `database_strong` | OpenAlex, PubMed, Crossref, Semantic Scholar, ORCID, or another reliable database marks author order or corresponding-author status. | Acceptable for batch screening and inclusion when identity is strong, but note source and caveats. |
| `official_cv` | Target's official CV/homepage lists the work under publications and role is clear from byline or notes. | Acceptable identity evidence; role still needs byline/correspondence support. |
| `inferred_only` | Last-author position, seniority, lab relationship, or field convention without explicit role evidence. | Do not include as corresponding/co-corresponding by default. |

For co-first authorship, actively check publisher footnotes, author contribution notes, PDF first page, JATS `contrib` metadata, and official article pages for phrases such as equal contribution or contributed equally. Do not infer co-first authorship from adjacent author order alone.

For corresponding and co-corresponding authorship, prefer publisher correspondence lines and target email evidence when a record is ambiguous. Database `is_corresponding=true` can support default inclusion when identity is strong. Keep the database source internally; do not add source-system notes to the main report table unless the user explicitly asks for audit details.

For speed, do not verify every database-strong OpenAlex first-author or corresponding-author record on publisher pages. Keep the database source and role evidence internally. Escalate to publisher/PDF only for the ambiguous-case queue from Phase 4.

### Phase 9 — Apply quality gates

Before finalizing, run these gates:

1. Identity Anchor Gate.
2. Timeline Gate.
3. Source Preflight Gate.
4. Candidate Coverage Gate.
5. Publication Existence Gate.
6. Institution Normalization Gate.
7. Identity Match Gate.
8. Authorship Role Gate.
9. Deduplication Gate.
10. Output Audit Gate.

If a gate fails, continue only with a lower confidence label and visible caveat. See `references/quality-gates.md`.

### Phase 10 — Produce final output

Default report sections:

1. Target identity summary.
2. Academic timeline.
3. Qualifying journal articles.

When the user's wording is "all papers", title the main deliverable as an all-qualifying-paper audit or equivalent language in the user's language. Briefly state that, under this skill, "all papers" means all verified first/co-first/corresponding/co-corresponding papers by the target scholar. Do not present middle-author-only coauthored records as the scholar's papers unless the user explicitly asks for a broader coauthorship appendix.

Default paper-list table:

```text
Year | Title | Journal | Scholar role
```

Readable report formatting:

- The main Markdown and Word reports are for human reading, not raw database debugging.
- In the main qualifying-paper list, show only these columns by default: year, paper title, journal, and scholar role.
- The `journal` column must contain only the journal or venue name, without volume, issue, page range, article number, source-system label, DOI, URL, or database name.
- The `journal` column must be verified before rendering. Never output a blank journal, `journal pending verification`, `venue pending verification`, `期刊信息待核验`, `来源待核验`, or similar placeholders in the default final table.
- Do not include DOI or source links in the default report table unless the user explicitly asks for identifiers or links.
- Do not include separate report sections for search coverage, limitations, caveats, notes, "supporting files", "companion files", evidence tables, rejection logs, Evidence Passport summaries, or preprint candidates.
- Do not include standalone preprints, discussion papers, or posted-content records in the default final report. Use them internally for discovery and merge them into the published article when a journal version exists. Mention a preprint only if the user explicitly asks for preprints or no published version exists and the user accepts provisional records.
- Do not display raw internal evidence strings such as `OpenAlex authorship.author_position=first`, `OpenAlex authorship.is_corresponding=true`, `Semantic Scholar author list position=first`, raw API field paths, JSON keys, cache IDs, or endpoint diagnostics in the main literature list.
- Keep raw role evidence, source-specific fields, IDs, API diagnostics, rejected candidates, and confidence metadata internal unless the user explicitly asks for audit details.
- Translate role labels and section headings into the language of the user's latest request. Do not hard-code Chinese role labels for every run.
- For Chinese reports, translate role labels naturally for first author, co-first author, corresponding author, co-corresponding author, first and corresponding author, and co-first and corresponding author.
- For English reports, use labels such as `first author`, `co-first author`, `corresponding author`, `co-corresponding author`, `first and corresponding author`, and `co-first and corresponding author`.
- Prefer grouping or sorting the final list by role and then by year, or by year with a clearly readable role column. Keep the table compact enough to fit Word pages.
- Prefer the deterministic helper `scripts/render_report.py` after the final article list is filtered. Provide it a small JSON payload with `report_title`, `executive_summary`, `identity_and_anchors`, `timeline`, `articles`, `output_md`, `output_docx`, and `language`; it fills the fixed Markdown and Word template.
- If using the helper is not possible, generate the Word report from the fixed template at `assets/authorship-audit-template.docx` when available. Copy the template, replace placeholders, populate the timeline and qualifying-paper tables, and then validate the resulting `.docx`.
- If the fixed template is unavailable or cannot be opened, create the Word report with `python-docx` using the same section order and table schema, then validate it before returning.

If the user asks for titles only, output only:

```text
1. [Year] Title
2. [Year] Title
3. [Year] Title
```

But still maintain internal evidence and caveats. Never present unverified titles as exhaustive.

If the user requests downloadable results, a formal report, or a test run of this skill on a scholar, generate reusable report artifacts when the environment supports file creation:

- Markdown report: `<scholar-name>-authorship-audit-report.md`;
- Word report: `<scholar-name>-authorship-audit-report.docx`.

Output location rules:

- If the user gives an output directory or file path, write the Markdown and Word reports there.
- If the user does not specify a location, write both reports under the current working directory at `outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/`.
- Do not write run outputs into the skill installation directory under `.codex/skills`.
- Keep the default output directory limited to the two report files unless the user explicitly asks for audit files.
- Do not create a sibling or child `tmp` folder in `outputs/scholar-authorship-auditor/` during a normal run. Temporary retrieval state must be ephemeral and cleaned before returning.

For normal full scholar runs and skill tests, produce only both Markdown and Word unless the user explicitly asks for a different format. Do not create CSV, XLSX, Evidence Passport JSON, rejection-log files, "companion files", or raw-evidence deliverables by default. The chat answer should summarize the result and link to the generated Markdown and Word files only. See `references/artifact-output.md`.

Before returning from a normal full/test run, audit the output root. If any intermediate files or directories such as `tmp`, raw API JSON, checkpoints, payload JSON, parsed evidence tables, or downloaded HTML remain under `outputs/scholar-authorship-auditor/`, remove them unless the user explicitly requested audit files. The final chat answer must link only the Markdown and Word reports by default.

Recommended report sections for `.md` and `.docx`:

1. Executive summary.
2. Scholar identity and anchors.
3. Education and career timeline.
4. Qualifying first/co-first/corresponding/co-corresponding journal articles.

Executive summary requirements:

- State the number of qualifying journal articles in the final table.
- State whether the scholar has published a qualifying article in the main journals `Nature` or `Science`.
- Count only the exact main journals `Nature` and `Science` for this item. Do not count Nature-family journals such as `Nature Geoscience`, `Nature Climate Change`, or `Nature Sustainability`, and do not count Science-family journals such as `Science Advances`, `Science Bulletin`, or `Science of the Total Environment`.
- If yes, name the qualifying article title(s), year(s), journal(s), and the scholar's role(s). If no, write that no qualifying main-journal `Nature` or `Science` article was identified under the fixed authorship rule.

Scholar identity and anchors requirements:

- Include the scholar's names and identifiers, then a detailed biography/timeline in prose or a compact table.
- The biography must list verified undergraduate, master's if applicable, Ph.D., postdoctoral, professor/PI/faculty, academy, and current-affiliation stages when publicly available.
- Use the user's language for labels and prose; preserve institution names and degree titles when official English names are the reliable form.

## Rejection log

Every candidate removed after raw recall must be logged with at least:

- title;
- year;
- source;
- reason;
- evidence;
- confidence.

Allowed reasons:

- `duplicate`;
- `same_name_author`;
- `affiliation_mismatch`;
- `insufficient_identity_evidence`;
- `middle_author_only`;
- `role_not_verified`;
- `non_paper_item`;
- `non_research_item`;
- `paywalled_or_unverifiable`;
- `publication_version_merged`;
- `unresolved_identity_collision`.

No silent exclusion is allowed.

## Completeness language

Use cautious language unless exhaustive conditions are met.

Allowed coverage labels:

- `high_coverage`: multiple independent sources checked, source preflight passed or failures were successfully retried, timeline stable, no major unresolved same-name cluster.
- `medium_coverage`: major sources checked, but some affiliations, roles, databases, publisher/PDF footnotes, or full author-works enumeration remain unavailable.
- `low_coverage`: sparse biography, common name, paywalled metadata, limited source access, or unresolved collisions.

Use "all papers", "all articles", or "all publications" only within this skill's fixed authorship definition. If coverage is not exhaustive, qualify the phrase. Prefer:

- "identified qualifying papers from the searched sources";
- "high-coverage all-qualifying-paper list";
- "provisional all-qualifying-paper list";
- "verified first/co-first/corresponding/co-corresponding paper list".

## Operating principles

- Timeline first, paper search second.
- Treat "all papers" as all first/co-first/corresponding/co-corresponding papers unless the user explicitly broadens the scope.
- Preflight only the sources needed for the sequential path before bulk retrieval; reserve retries for OpenAlex and critical identity anchors.
- Broad candidate retrieval, then strict identity and role filtering.
- Use target-author affiliation, not coauthor affiliation.
- Normalize institutions before matching.
- Treat +2 year affiliation lag as moderate evidence unless corroborated.
- Verify co-first, equal-contribution, corresponding, and co-corresponding roles from article-level evidence when possible.
- Do not assume last author equals corresponding author.
- Merge duplicate versions.
- Preserve raw source responses separately from slim evidence tables when feasible.
- Keep raw source responses and slim evidence tables internal or ephemeral; do not leave them in the final output tree by default.
- Log exclusions.
- Track source coverage and unresolved uncertainty internally when useful; do not add default coverage or limitation sections to the generated reports.
- Generate only Markdown and Word reports for full/test runs when file creation is available, unless the user explicitly asks for extra audit files.
- Never fabricate titles, affiliations, years, venues, DOIs, or roles.
