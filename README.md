# Companion data — gendered variation in a Finnish healthcare AI assistant

Supporting material for the case report [*A case report on gendered biases in a Finnish
healthcare AI assistant*](https://www.medrxiv.org/content/10.64898/2026.04.09.26350383v1) (Luisto, Snell, Vartiainen, Sanmark, Äyrämö).

**NOTE: This public companion repo was built with the assistance of Claude Code. Contents have been checked by the author but the language used reeks of LLM.**

The system studied in the paper was a development version of a retrieval-augmented assistant built for
two Finnish wellbeing services counties. The project was discontinued for reasons unrelated
to this study, and the system no longer exists: it cannot be re-queried, and the retrieval
indexes were not exported before they were destroyed. What is published here is therefore a
complete record of the outputs of the **main configuration** — the three generation runs the
paper's findings rest on — and not a reproducible pipeline. The retrieval ablation runs
reported in the paper are not represented here: neither their responses nor their automated
screening results are included.

## What is here

| Path | Contents |
|---|---|
| `queries/` | All 40 queries drawn from the system's end-user testing logs, with the three gendered variants generated for the 36 found suitable for gendering. JSON and CSV, plus `queries_40_translated.json` with English translations of every query **and of all three gendered variants**, per-query notes on how the gender marker was attached, and identifiers linking each query to the paper and to the response files. `crosswalk.csv` is the same identifier mapping on its own. |
| `prompts/` | The two prompts used in the study: the answer-generation prompt sent to the assistant, and the LLM-as-a-judge screening prompt, together with the exact API parameters used for every judge call. |
| `responses/` | Every response from the main configuration: 3 independent generation runs × 36 queries × 3 gendered variants = 324 responses, in Finnish. One JSONL file per run. |
| `judge_output/` | The automated screen's full structured output for every query in every run, plus a CSV of the label assigned to each query in each run. |
| `tables/` | The tables reported in the paper, as CSV, covering the main configuration. Automated screening results for the retrieval ablation conditions are withheld. |
| `manifest.json` | Run identifiers and counts. |

### Record formats

`responses/responses_run{1,2,3}.jsonl` — one JSON object per line:

```
query_id, original_question, timestamp,
variants: { male|female|neutral: { query, answer, word_count, completion_tokens, finish_reason } }
```

`judge_output/judge_run{1,2,3}.jsonl` — one JSON object per line:

```
query_id, original_question, screened_by, label, summary, description,
specific_differences[], response_quality_comparison{}, recommendation
```

`label` is one of `none`, `low`, `medium`, `high`. The screen saw the three variants of one
query together and returned a single label for that triplet; it was not run more than once
per triplet, so these data describe variation between generation runs and not the
reliability of the screen itself. The exact API parameters used for every judge call are in
`prompts/llm_judge_api_parameters.json`.

## Identifying a query

Three identifiers are in play and they do not coincide:

| Identifier | Range | Where it is used |
|---|---|---|
| `source_list_number` | 1–40 | the full extracted query list; the order of `queries_40.json` and `queries_40.csv` |
| `manuscript_number` | 1–36 | the paper's appendix, which numbers only the included queries |
| `query_id` | hash | `responses/*.jsonl` and `judge_output/*.jsonl` |

The two numberings are offset because the four excluded queries sit at source positions 2,
20, 31 and 33. Ear congestion, for instance, is source 12, manuscript 11, and `2ba6f22f` in
the response files. All three are carried on every entry in `queries_40_translated.json`, and
`queries/crosswalk.csv` is the mapping on its own.

## Redactions

The assistant sometimes reproduced named municipal staff and their direct contact details
from the retrieved source documents. Before the release of this companion repository we replaced those with role-describing
placeholders:

| Placeholder | Occurrences |
|---|---|
| `[INTEGRATION COORDINATOR NAME REDACTED]` | 7 |
| `[HOUSING COORDINATOR NAME REDACTED]` | 6 |
| `[NURSE NAME REDACTED]` | 4 |
| `[PHONE REDACTED]` | 11 |

These affect two queries — `5b8527af` (*pakolaispalvelut*, refugee services) and `aff7fe94`
(*Luhangan neuvolan yhteyshenkilö*, the contact person for the Luhanka clinic) — in both the
response files and the two screening records that quote them. 28 replacements in total.

The redaction preserves the finding. Where the paper notes that one gendered variant received
a specific named contact while the others did not, the placeholders make that contrast equally
visible; only the individual's identity is removed.

Institutional switchboard numbers (the `014` Jyväskylä-area numbers for the Keuruu family
clinic, Laukaa health services and a fertility clinic) are **not** redacted. They are published
organisational contacts rather than personal ones.

This repository has no history prior to its first commit, so the unredacted text was never
published here.

## What is not here, and why

- **The retrieved documents.** The passages the assistant retrieved and used as context are
  not released. They are the property of the participating wellbeing services counties and
  include internal clinical guideline material that is not ours to distribute.
- **Retrieval configuration.** Index names, retrieval parameters and the internal routing
  rules are not released.
- **The system itself.** The application source was is likewise not ours to share.

`tables/table7_retrieval_overlap.csv` reports *aggregate* overlap between the document sets
retrieved for different gendered variants. It contains no document titles, URLs or text, and
is included because it supports a claim in the paper.

## Scope and caveats

- Everything here comes from the **main configuration**: `gpt-4o-mini` as the generating
  model, GPT-5 as the screening model, three generation runs. The retrieval ablation runs
  reported in the paper are not part of this release — neither their responses nor their
  screening results. Where a table has a row for an ablation condition, its screening label
  reads `not released`; the remaining columns in those rows are derived from the responses
  rather than from the screen.
- Model aliases were called without pinning, and the dated snapshots the API resolved them
  to were not recorded. The runs were executed on 6 October 2025.
- The source queries were typed by healthcare staff — the nurses and secretaries who would
  have been the system's users — during end-user testing between 4 and 21 August 2025, and
  were recovered afterwards from the system logs. They describe constructed example cases
  rather than real patient encounters, so no patient data is involved; we screened them for
  identifiers before analysis and found none. They are therefore neither machine-generated
  nor drawn from clinical records: they are realistic queries authored by the prospective
  users themselves, which is why the paper treats them as more representative than a
  researcher-written benchmark would be.
- Every query and all three of its gendered variants are translated into English in
  `queries/queries_40_translated.json`. The **responses** are in Finnish and are not
  translated; translating 324 clinical responses was beyond what we could verify to a
  standard we would be willing to publish. The one response triplet discussed at length in
  the paper is reproduced there in Finnish.
- The three generation runs are genuinely independent: no response was identical across runs.

## Citing

Please cite the paper. If you use these files directly, please also note the run identifiers
in `manifest.json`, since the three runs are not interchangeable.
