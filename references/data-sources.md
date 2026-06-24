# Data sources and search strategy

No single source is complete. Use a source stack selected by field, but query secondary sources only when the OpenAlex batch spine or recall-gap check leaves a concrete question unresolved.

The purpose of searching broadly is candidate discovery only. The final list must still include only papers where the target scholar is verified as first author, co-first author, corresponding author, or co-corresponding author.

## Source priority

### Identity and timeline sources

Use first for the scholar's identity and career timeline:

1. official faculty or lab page;
2. official CV;
3. personal homepage;
4. ORCID education and employment;
5. dissertation/thesis record;
6. university news and award pages;
7. grant/project pages;
8. conference biographies;
9. LinkedIn or ResearchGate only as secondary support;
10. local-language institutional pages.

### Publication spine sources

Use first for candidate paper lists:

1. OpenAlex author works, after the target author ID has been resolved;
2. official CV/publication page;
3. ORCID works;
4. Google Scholar profile as a lightweight recall-gap check;
5. Crossref DOI metadata only for missing DOI, venue, date, or version clarity;
6. Semantic Scholar author page only for OpenAlex gaps or unresolved same-name/citation-network cases;
7. field-specific databases only when the field/source gap requires them.

Treat self-curated or automatically merged profiles as high-value but not automatically correct.

## Source selector by field

| Field | Recommended sources |
|---|---|
| Multidisciplinary | OpenAlex, Crossref, ORCID, Google Scholar, Semantic Scholar |
| Biomedicine/life sciences | PubMed, Europe PMC, ORCID, OpenAlex, publisher pages |
| Computer science | dblp, Semantic Scholar, ACM, IEEE, arXiv, OpenAlex |
| Physics/astronomy | arXiv, INSPIRE, NASA ADS, ORCID, OpenAlex |
| Chemistry/materials | ACS, RSC, Wiley, Springer, Nature, Elsevier, OpenAlex, Crossref |
| Engineering | IEEE, ACM, Elsevier, Springer, OpenAlex |
| Mathematics | zbMATH, MathSciNet if available, arXiv, publisher pages |
| Local-language output | CNKI, Wanfang, Baidu Scholar, AMiner, and institutional pages in relevant local languages |
| Preprints | arXiv, bioRxiv, medRxiv, ChemRxiv, SSRN, Research Square |

## What each source is good for

### ORCID

Best for identity when maintained by the scholar. It can contain works, education, employment, funding, and identifiers. It is not guaranteed complete.

Use ORCID as a verified spine when the record is clearly maintained by the target. Still verify author role because ORCID works often do not encode first/co-first/corresponding status.

### Google Scholar

High recall and useful for discovering titles. It is not an official API source and can include polluted same-name papers. Always audit by timeline, affiliation, author list, and topic.

### OpenAlex

Best open source for structured work metadata, authorships, author IDs, institutions, author position, and sometimes corresponding author status.

Useful fields:

- Work ID;
- DOI;
- publication year/date;
- authorships;
- `raw_author_name`;
- `author_position`;
- `is_corresponding` when available;
- institutions;
- raw affiliation strings;
- venue/source.

Use OpenAlex as the default batch spine for candidate generation and initial role filtering. After identity is strong, accept target `author_position=first` and target `is_corresponding=true` as database-strong role evidence for the default report. Use publisher/PDF checks only for ambiguous or high-stakes boundary cases, not for every OpenAlex-positive record.

For speed, use OpenAlex field selection (`select` or equivalent) and keep only fields needed for local screening: work ID, DOI, title, year/date, type, source/venue, IDs, authorships, author IDs, author order, correspondence flag, institutions, and raw affiliation strings. Avoid abstracts, concepts, references, citation lists, and per-work detail requests unless a record enters the ambiguous-case queue.

### Crossref

Good for DOI, title, venue, publication date, and broad metadata. Author affiliations and corresponding-author data are incomplete. Do not rely on Crossref alone for identity disambiguation. Do not run Crossref for every OpenAlex record when OpenAlex already provides DOI, venue, and date; query it only for missing or conflicting metadata.

### Semantic Scholar

Good for author pages, paper IDs, citation graph, coauthor networks, abstracts, and computer science/AI coverage. Author profiles can still merge same-name people. Do not run a broad Semantic Scholar audit by default after a successful OpenAlex batch plus one recall-gap check; use it only for OpenAlex-missing titles, same-name collisions, or fields where Semantic Scholar is clearly the better spine.

### PubMed and Europe PMC

Best for biomedical literature. Useful for affiliations, author order, publication types, PMIDs/PMCIDs, and sometimes corresponding emails. Check whether affiliation fields are per-author or all-author level.

### dblp

Useful for computer science. It often separates same-name authors into distinct pages and is strong for conferences. It may not contain affiliations or corresponding-author information.

### Publisher pages and PDFs

Best for final role verification of ambiguous cases. Use them to check:

- author order;
- co-first/equal-contribution footnotes;
- corresponding author lines;
- emails;
- raw affiliations;
- present-address notes;
- publisher/JATS metadata.

Do not open publisher pages or PDFs for every paper in an OpenAlex author-works batch. Reserve them for possible co-first/equal-contribution records, missing or conflicting correspondence metadata, title/DOI/version conflicts, records discovered outside OpenAlex, same-name collisions, main-journal `Nature` or `Science` records, and official-CV/OpenAlex role conflicts.

When OpenAlex shows a target first-author or corresponding-author role with strong identity and no conflict, keep the source evidence internally and include the record without opening the publisher page.

### Local-language sources

For scholars with important local-language records, search:

- native-script name + institution;
- Chinese institution aliases;
- CNKI;
- Wanfang;
- Baidu Scholar;
- AMiner;
- institutional Chinese pages;
- funding project pages.

## Query strategy

For each scholar, run only the queries needed to resolve identity, recall gaps, or ambiguous records. Prefer identifier and profile queries over broad name searches. Combine:

- name variants;
- native-script name;
- current institution;
- past institutions;
- field keywords;
- advisor/lab name;
- representative paper title;
- ORCID or author ID;
- email handle if public.

Example patterns:

```text
"Xiaoming Wang" "Tsinghua University" catalysis
"X. Wang" "Stanford" "MIT" materials
"<native-script name>" "<native-script institution>" papers
"Xiaoming Wang" ORCID
"Xiaoming Wang" "corresponding author"
```

## Minimum source coverage

For a full run, attempt at least:

- one identity/timeline source;
- OpenAlex author-works batch enumeration when quota and identity resolution allow;
- one profile/spine source such as ORCID, official CV, or Google Scholar as a recall-gap check;
- one publisher/PDF-level check only for ambiguous role evidence where needed.

For common names, check at least two independent bibliographic sources plus timeline evidence.

If OpenAlex enumeration succeeds, one independent profile/spine recall-gap check finds no missing qualifying titles, and the remaining final records have no ambiguous identity or role questions, stop broad retrieval. Do not add Crossref, Semantic Scholar, PubMed, local-language, or publisher checks only to inflate source count.

## Source logging

Record all searched sources in the Evidence Passport:

- source name;
- query string;
- date searched;
- number of candidates found;
- number included;
- number excluded;
- limitations.
