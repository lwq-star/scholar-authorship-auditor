# Institution normalization

Affiliation strings are messy. Normalize institution names before matching them to the scholar timeline.

## Goals

For each paper, extract and normalize the target author's own affiliation, not a coauthor's affiliation.

Output:

- raw affiliation string;
- department/lab;
- institute/school/hospital;
- parent university or organization;
- city;
- country;
- canonical institution name;
- local-language name;
- aliases;
- ROR ID if available;
- OpenAlex institution ID if available;
- match level;
- confidence.

## Parsing procedure

1. Identify the target author's superscript or affiliation marker.
2. Resolve that marker to the target's own affiliation block.
3. If the target has multiple affiliations, record all.
4. Separate department/lab from parent institution.
5. Extract city and country.
6. Normalize institution name and aliases.
7. Compare normalized institution to the scholar timeline.
8. Preserve raw text for audit.

## Matching levels

| Level | Example | Confidence impact |
|---|---|---|
| Exact entity | Same ROR/OpenAlex institution ID or exact official name. | Strong |
| Alias/historical name | `MIT` vs `Massachusetts Institute of Technology`; old university name. | Strong if date-compatible |
| Parent-child | Affiliated hospital vs parent university; institute inside national academy. | Moderate unless timeline also lists child entity |
| Department mismatch | Same university, different department. | Strong or moderate depending on field/lab continuity |
| Umbrella-only | `Chinese Academy of Sciences`, `CNRS`, `University of California`. | Moderate/weak; resolve institute/campus if possible |
| City/country only | Same city/country but no institution match. | Weak, not enough alone |

## Affiliated hospitals and medical schools

Handle these carefully:

- If timeline lists a university and paper lists its affiliated hospital, match `moderate` unless official profile confirms the hospital appointment.
- If timeline lists a hospital and paper lists the parent university, match `moderate` or `strong` depending on appointment evidence.
- For medical scholars, hospital affiliation may be the primary appointment; do not collapse all hospitals into the parent university without evidence.

## Research institutes and umbrella organizations

Examples needing specificity:

- Chinese Academy of Sciences;
- Max Planck Society;
- CNRS;
- Helmholtz Association;
- University of California system;
- State Key Laboratory networks;
- national labs;
- joint institutes.

Prefer the most specific institution/lab/campus available. An umbrella match alone is not enough for strong identity confidence.

## Historical names and mergers

Record historical names and date ranges:

- institution renamed;
- hospital merged;
- department reorganized;
- institute moved parent organization;
- university adopted English-name change.

A historical name can be a strong match if the date aligns.

## Local-language institution names

Record local-language and English names when available:

- native-script official name / `Tsinghua University`;
- native-script official name / `Peking University`;
- native-script official name / `University of Chinese Academy of Sciences`;
- native-script institute alias / `Institute of Chemistry, Chinese Academy of Sciences`.

Search both native-script and English forms. Native-script name plus institution is often high precision.

## Present address and current address

Some papers include:

- affiliation where work was done;
- present address;
- current address;
- additional affiliation after a move.

For disambiguation, affiliation where work was done is usually more relevant to the timeline at submission. Present/current address may explain why a future institution appears before official appointment or why old work appears after a move.

Record both when available.

## Common mistakes

Avoid:

- matching on coauthor affiliation;
- matching only by department name;
- treating every campus in a university system as same;
- treating all affiliated hospitals as identical to parent university;
- ignoring local-language names;
- ignoring historical names;
- making +2 year old-affiliation matches strong without corroboration.
