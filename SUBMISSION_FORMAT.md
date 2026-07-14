# Submission Format

## Status

This document defines the proposed JSONL submission format for schema version
`0.1-draft`. Final run limits, filenames, and delivery instructions will be
announced separately.

## File Requirements

- UTF-8 encoded JSONL.
- One record for every formal-run `case_id`.
- Exactly one JSON object per line.
- No duplicate, missing, or unknown `case_id` values.
- Records should follow the same order as the formal-run input file.

## Submission Record

| Field | Type | Required | Description |
|---|---|---:|---|
| `schema_version` | string | Yes | Submission schema version |
| `run_id` | string | Yes | Stable identifier for the submitted run |
| `case_id` | string | Yes | Case identifier copied from the input |
| `evidence_used` | enum | Yes | `R`, `I`, or `RI` |
| `validity` | enum | Yes | `valid` or `invalid` |
| `errors` | array | Yes | Predicted error objects |
| `corrected_causal_explanation` | string | Yes | Complete corrected explanation |
| `confidence` | number | Yes | Confidence between 0 and 1, inclusive |

### Predicted error object

| Field | Type | Required | Description |
|---|---|---:|---|
| `start` | integer | Yes | Zero-based inclusive Unicode character offset |
| `end` | integer | Yes | Zero-based exclusive Unicode character offset |
| `text` | string | Yes | Exact input text covered by the span |
| `type` | enum | Yes | Official hallucination type |

## Valid Prediction

For a prediction of `valid`:

- `errors` must be `[]`; and
- `corrected_causal_explanation` must copy the input
  `causal_explanation` unchanged.

## Invalid Prediction

For a prediction of `invalid`:

- `errors` must contain at least one predicted error;
- every span must match the input explanation exactly; and
- `corrected_causal_explanation` must contain the complete corrected text.

For `EVID-OMIT` and `DX-OMIT`, a submitted error may use `start == end` and an
empty `text` value to mark the insertion point of omitted content. Other error
types must use non-empty spans.

## Evidence Metadata

All records in one submitted run should normally use the same
`evidence_used` value:

- `R`: report only;
- `I`: image or images only; or
- `RI`: report and image or images.

If mixed evidence usage within one run is permitted later, the organizers will
state that explicitly in the final submission instructions.

## Confidence

`confidence` represents the system's confidence in its overall case-level
output, including the validity decision and associated verification result.
It must satisfy:

```text
0.0 <= confidence <= 1.0
```

## Example

See [examples/sample_submission.jsonl](./examples/sample_submission.jsonl).

## Validation Checklist

Before submission, verify that:

1. the file parses as UTF-8 JSONL;
2. every formal-run case occurs exactly once;
3. all required fields are present;
4. all spans use the official offset convention;
5. `text` exactly matches the indexed input substring;
6. valid predictions have an empty `errors` array;
7. confidence values are within `[0, 1]`; and
8. corrections do not introduce unsupported claims.
