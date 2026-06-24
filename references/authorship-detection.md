# Authorship-role detection

This reference defines how to decide whether the target scholar qualifies as first author, co-first author, corresponding author, or co-corresponding author.

## Core rule

A paper qualifies by default only if the target scholar is one of:

- `first`;
- `co_first`;
- `corresponding`;
- `co_corresponding`;
- `first_and_corresponding`;
- `co_first_and_corresponding`;
- `co_first_and_co_corresponding`.

Middle-author-only papers do not qualify.

## Role labels

Use these labels consistently:

| Label | Meaning | Default inclusion |
|---|---|---:|
| `first` | Target is printed first in the byline. | Yes |
| `co_first` | Target is explicitly in the equal-contribution first-author group. | Yes |
| `corresponding` | Target is explicitly marked as corresponding author. | Yes |
| `co_corresponding` | Multiple corresponding authors are listed and target is one of them. | Yes |
| `first_and_corresponding` | Target is both first and corresponding. | Yes |
| `co_first_and_corresponding` | Target is co-first and corresponding. | Yes |
| `co_first_and_co_corresponding` | Target is co-first and one of multiple corresponding authors. | Yes |
| `equal_contribution_all_authors` | The paper states all authors contributed equally, without identifying a first-author group. | No, unless user asks. |
| `contribution_statement_only` | A CRediT or author-contribution section describes substantial contribution, but no first/corresponding status. | No |
| `possible_corresponding` | Email or metadata hints at correspondence but not enough to confirm. | No by default |
| `role_unconfirmed` | Role cannot be verified. | No by default |

## Evidence levels

| Level | Evidence type | Use |
|---|---|---|
| A | Publisher HTML, PDF first page, JATS/XML, article footnote, or official publication page explicitly links target to role. | Final confirmation. |
| B | OpenAlex, PubMed, Europe PMC, Crossref, Semantic Scholar, or other structured metadata indicates author position or correspondence. | Good for default batch screening and inclusion when identity is strong; confirm only ambiguous or high-stakes boundary cases. |
| C | Official CV or ORCID lists paper, and byline order is visible elsewhere. | Useful identity evidence; role still needs byline/footnote verification. |
| D | Conventional inference such as last author = PI/corresponding. | Hint only; never final proof. |

## First author

The target is `first` if their name is printed first in the byline.

Caveats:

- Some fields use alphabetical author order. In mathematics, economics, high-energy physics, and some theory-heavy areas, first position may not indicate lead contribution. Still record the printed position, but note the convention.
- For consortium papers with group authorship, verify whether the target is named in the main byline or only in a collaborator appendix.

## Co-first author / equal-contribution first author

The target is `co_first` only if the article explicitly links the target to the equal-contribution first-author group.

Acceptable evidence:

- “These authors contributed equally.”
- “Equal contribution.”
- “Contributed equally to this work.”
- A shared dagger, hash, numbered note, or other footnote symbol attached to the target and the first-author group.
- Publisher/JATS/XML metadata where `equal-contrib="yes"` clearly applies to the first-author group.

Parsing requirement:

1. Identify the symbol or note attached to each author.
2. Resolve the symbol/note text.
3. Confirm that the target author carries the equal-contribution symbol.
4. Confirm that the target is part of the lead-author/equal-contribution group, not merely covered by a general contribution statement.

Examples:

```text
A†, B†, C, D*
† These authors contributed equally.
* Correspondence to D.
```

Result:

- A = `co_first`
- B = `co_first`
- D = `corresponding`

Non-qualifying example:

```text
Author contributions: All authors contributed equally to writing and reviewing the manuscript.
```

Result:

- `equal_contribution_all_authors` or `contribution_statement_only`
- Do not treat as co-first unless the user explicitly wants that broader definition.

## Corresponding author

The target is `corresponding` if the article explicitly identifies the target as responsible for correspondence.

Acceptable evidence:

- “Correspondence to: [target]”
- “Corresponding author: [target]”
- “Corresponding authors: [target], [other]”
- “To whom correspondence should be addressed” linked to target.
- Asterisk, envelope, dagger, or numbered footnote linked to target and resolved as correspondence.
- Target email appears in a correspondence block.
- Publisher/JATS/PDF metadata explicitly links target to correspondence.

Do not infer corresponding author from last-author position. Last-author position is a field convention in some disciplines, not a role marker.

## Co-corresponding author

If multiple corresponding authors are listed and the target is any one of them, label as `co_corresponding`.

Common patterns:

```text
* Correspondence to X and Y
Corresponding authors: X, Y
X* and Y*; emails: x@..., y@...
```

If target email is listed but no correspondence language is present, label `possible_corresponding`, not `corresponding`.

## Source-specific notes

- OpenAlex may expose `author_position` and `is_corresponding`. In the default OpenAlex-batch-first workflow, use target `author_position=first` and target `is_corresponding=true` as database-strong evidence when identity is strong.
- Crossref usually has author order but not reliable corresponding-author status.
- PubMed and Europe PMC can surface affiliations and sometimes corresponding email, especially in biomedicine.
- Google Scholar does not reliably expose author role.
- Publisher pages and PDFs are preferred for equal-contribution and corresponding-author footnotes, but they are reserved for ambiguous cases rather than every OpenAlex-positive record.

## Role decision procedure

For each identity-matched candidate:

1. Read the author byline.
2. Identify target's printed name and author position.
3. Parse footnote symbols and notes.
4. Look for equal-contribution statements.
5. Look for correspondence statements and emails.
6. Assign role label.
7. Assign role evidence level.
8. If role remains uncertain, move to ambiguous/provisional list rather than final confirmed list.
