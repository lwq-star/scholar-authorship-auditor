# Evidence Passport

The Evidence Passport is the run-level audit record for this skill. It prevents silent assumptions, makes the final qualifying-paper list auditable, and enables later continuation.

## Purpose

Maintain the Evidence Passport throughout the run. It records:

- target identity;
- timeline evidence;
- search strategy;
- raw candidates;
- deduplication decisions;
- institution normalization;
- identity-matching decisions;
- authorship-role decisions;
- ambiguous records;
- rejection log;
- coverage statement;
- artifact outputs.

## Run metadata

```yaml
run_metadata:
  skill_name: scholar-authorship-auditor
  skill_version: "0.5.0"
  run_date: "YYYY-MM-DD"
  run_type: full
  fixed_inclusion_rule: first_or_co_first_or_corresponding_or_co_corresponding
  data_access_level: raw-to-verified
  user_inputs:
    name: null
    institution_anchor: null
    field_anchor: null
    orcid: null
    profile_url: null
    representative_paper: null
  artifact_outputs:
    markdown_report: true
    word_report: true
    extra_audit_files: false
  coverage_label: low_coverage | medium_coverage | high_coverage
```

## Target identity block

```yaml
target_scholar:
  canonical_name: null
  native_script_names: []
  name_variants: []
  identifiers:
    orcid: null
    google_scholar: null
    openalex_author_id: null
    semantic_scholar_author_id: null
    other_author_ids: []
  current_affiliation: null
  known_past_affiliations: []
  field_keywords: []
  advisors: []
  recurring_coauthors: []
  email_domains: []
  official_sources: []
```

## Timeline block

```yaml
timeline:
  - period: "YYYY-YYYY"
    start_year: null
    end_year: null
    institution_raw: null
    institution_normalized: null
    institution_ids:
      ror: null
      openalex: null
    role: null
    department_or_lab: null
    location: null
    source: null
    certainty: confirmed | inferred | uncertain
    notes: null
```

## Search log

```yaml
search_log:
  - source: OpenAlex
    query: null
    date_searched: "YYYY-MM-DD"
    name_variant: null
    institution_filter: null
    field_filter: null
    raw_results_count: 0
    candidates_added: 0
    limitations: null
```

## Candidate record schema

```yaml
candidate_paper:
  candidate_id: null
  title: null
  normalized_title: null
  year: null
  best_available_date:
    type: submitted | accepted | online_first | final_publication | year_only
    value: null
  venue: null
  publication_type: null
  identifiers:
    doi: null
    pmid: null
    pmcid: null
    arxiv_id: null
    openalex_work_id: null
    semantic_scholar_paper_id: null
  source_records: []
  authors:
    full_list_available: true
    target_printed_name: null
    target_position: null
    total_authors: null
  affiliations:
    target_raw_affiliations: []
    normalized_institutions: []
    affiliation_source: null
  identity_evidence:
    confidence: confirmed | strong | moderate | ambiguous | excluded
    matched_timeline_phase: null
    evidence_items: []
    conflicts: []
  role_evidence:
    role: first | co_first | corresponding | co_corresponding | first_and_corresponding | co_first_and_corresponding | co_first_and_co_corresponding | equal_contribution_all_authors | contribution_statement_only | possible_corresponding | role_unconfirmed
    confidence: confirmed | strong | moderate | ambiguous | excluded
    evidence_source: publisher_page | pdf | jats_xml | pubmed | europe_pmc | openalex | official_cv | other
    evidence_text_summary: null
    needs_manual_review: false
  decision:
    status: include | ambiguous | exclude | duplicate_merged
    reasons: []
    notes: null
```

## Rejection log

```yaml
rejection_log:
  - candidate_id: null
    title: null
    year: null
    source: null
    reason:
      - duplicate
      - same_name_author
      - affiliation_mismatch
      - insufficient_identity_evidence
      - middle_author_only
      - role_not_verified
      - non_paper_item
      - non_research_item
      - paywalled_or_unverifiable
      - publication_version_merged
      - unresolved_identity_collision
    evidence: null
    confidence: null
```

## Coverage statement

```yaml
coverage:
  label: high_coverage | medium_coverage | low_coverage
  sources_checked: []
  unresolved_timeline_gaps: []
  unresolved_same_name_clusters: []
  role_verification_limitations: []
  paywalled_or_unavailable_items: []
  completeness_statement: null
```
