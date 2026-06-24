# Author disambiguation

The core problem is that a name is not an identifier. This reference describes how to separate the target scholar from same-name authors.

## Identity profile

Before trusting publication results, create an identity profile containing:

- full name;
- native-script name;
- romanized variants;
- initials;
- name-order variants;
- known name changes;
- ORCID;
- Google Scholar profile;
- OpenAlex Author ID;
- Semantic Scholar Author ID;
- external database author IDs if available;
- email handle/domain;
- education and employment timeline;
- advisor and lab;
- recurring coauthors;
- research topics;
- grants/projects;
- institution aliases.

## Name-variant enumeration

Search all plausible variants:

- `Given Family`
- `Family Given`
- `G. Family`
- `G.-M. Family`
- `Family G`
- `Family GM`
- native-script name
- hyphenated and unhyphenated given names
- pinyin, Wade-Giles, Korean/Japanese romanization variants
- English name or anglicized name
- maiden/married/changed names

For CJK names, the native-script form plus institution is often much higher precision than romanized name alone.

## Academic timeline

Build a table from undergraduate education to current appointment when public evidence exists:

| Period | Institution | Role | Department/Lab | Location | Evidence source | Certainty |
|---|---|---|---|---|---|---|

Rules:

- Prefer primary and dated sources: official CV, faculty page, ORCID employment/education, dissertation record, official news, grant profile.
- Use secondary sources only to fill gaps: LinkedIn, ResearchGate, conference bios, lab pages, media interviews, local-language databases.
- Mark inferred dates as inferred.
- Never fabricate unknown education or employment history.
- Record transition years precisely when possible.

## Affiliation-time matching

For each candidate paper, extract:

- best available paper date;
- target author's own printed affiliation;
- normalized institution;
- target author IDs;
- coauthors;
- topic signals.

Use the best available date in this order:

1. submitted/received date;
2. accepted date;
3. online-first or early-access date;
4. final issue publication date;
5. publication year only.

### Classification

| Classification | Rule |
|---|---|
| `confirmed` | Official CV/ORCID/publisher/PDF/identifier explicitly ties target to the paper and role. |
| `strong` | Target affiliation matches a timeline institution active at the paper date, or within 1 year after departure with topic/coauthor continuity. |
| `moderate` | Target affiliation matches a former institution within 2 years after departure; or a future institution within 1 year before start with visiting/joint/early appointment evidence; or affiliation missing but at least two secondary fingerprints match. |
| `ambiguous` | Same-name collision, missing affiliation, conflicting author IDs, overlapping institution/field/time, or insufficient evidence. |
| `excluded` | Institution, author ID, field, location, era, or role clearly conflicts with target. |

### Important rule for ±2 years

The +2 year affiliation lag window is a recall and moderate-evidence window. It is not an automatic strong-match rule.

- In-period match: usually strong.
- 0-1 year after departure: strong or moderate depending on coauthor/topic continuity.
- 2 years after departure: moderate only; require corroboration.
- More than 2 years after departure: usually exclude unless official CV, ORCID, or decisive identifier confirms.
- 1 year before start: weak/moderate only if visiting, joint appointment, early appointment, or present-address evidence exists.
- More than 1 year before start: usually exclude.

## Secondary fingerprints

Use these when affiliation is missing or ambiguous:

- ORCID on article;
- author ID linkage;
- target email or domain;
- advisor or lab head as coauthor;
- recurring coauthors;
- topic/method continuity;
- funding/grant continuity;
- institution city/country;
- dissertation/lab/project link;
- acknowledgments or author notes.

Suggested thresholds:

- Missing affiliation: require at least two strong secondary fingerprints.
- Conflicting affiliation: require one decisive identifier or at least three strong secondary fingerprints.
- Same institution + same name + same era: require advisor/lab/topic/ORCID/email evidence. If unresolved, report ambiguous.

## Exclusion patterns

Exclude or flag as ambiguous when:

- target affiliation belongs to an institution never in the timeline;
- field is unrelated and coauthor network is disjoint;
- paper predates target's plausible academic activity;
- author ID belongs to another same-name person;
- target appears only in a consortium list or collaborator appendix and is not a qualifying first/co-first/corresponding/co-corresponding author;
- role is middle author only and not corresponding.

## Failure modes

Be explicit about failure modes:

- two same-name scholars at same institution and same period;
- sparse biography;
- paywalled author footnotes;
- incomplete affiliation metadata;
- database author-profile contamination;
- name changes or inconsistent romanization;
- institutional mergers or historical names.
