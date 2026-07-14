# JSONL Schema

## Status

This document defines schema version `0.1-draft`. The organizers should assign
a stable release version after validating the first dataset export and sample
submission.

## General JSONL Requirements

- Files are UTF-8 encoded.
- Each non-empty line is one valid JSON object.
- Files do not contain an outer JSON array.
- Each `case_id` occurs exactly once per file.
- Field names are case-sensitive.
- Unknown fields should be rejected by the official validator unless a later
  schema version explicitly permits them.

## Input Record

| Field | Type | Required | Description |
|---|---|---:|---|
| `schema_version` | string | Yes | Schema version, initially `0.1-draft` |
| `case_id` | string | Yes | Stable unique task case identifier |
| `report_ref` | object | Yes | Reference to the corresponding report |
| `image_refs` | array | Yes | One or more image-reference objects |
| `causal_explanation` | string | Yes | Explanation to verify |

### `report_ref`

| Field | Type | Required | Description |
|---|---|---:|---|
| `source` | string | Yes | Normally `MIMIC-CXR`; `SYNTHETIC` in examples |
| `report_id` | string | Yes | Stable report identifier |
| `url` | string or null | Yes | Authorized-access URL; null only in synthetic examples |

### `image_refs[]`

| Field | Type | Required | Description |
|---|---|---:|---|
| `source` | string | Yes | Normally `MIMIC-CXR`; `SYNTHETIC` in examples |
| `dicom_id` | string | Yes | Stable DICOM identifier |
| `url` | string or null | Yes | Authorized-access URL; null only in synthetic examples |

`image_refs` must contain at least one element. The list order must remain
stable across releases.

## Labeled Record

A labeled record contains every input field plus:

| Field | Type | Required | Description |
|---|---|---:|---|
| `validity` | enum | Yes | `valid` or `invalid` |
| `errors` | array | Yes | Zero or more error objects |
| `corrected_causal_explanation` | string | Yes | Complete gold corrected explanation |

### `errors[]`

| Field | Type | Required | Description |
|---|---|---:|---|
| `error_id` | string | Yes | Unique within the case, such as `E1` |
| `start` | integer | Yes | Zero-based inclusive Unicode character offset |
| `end` | integer | Yes | Zero-based exclusive Unicode character offset |
| `text` | string | Yes | Exact text covered by the span |
| `type` | enum | Yes | Official hallucination code |
| `replacement` | string | Yes | Replacement text; may be empty for deletion |

Allowed `type` values are:

```text
ADD-PATH
ADD-DEVICE
ADD-REC
LOC-LAT
SEV-CHG
NEG-FLIP
CAUSE-WRONG
DDX-WRONG
EVID-OMIT
DX-OMIT
TERM-MINOR
CONTRA
FLUENCY
CERT-FLIP
```

## Span Rules

Offsets use Unicode characters, not UTF-8 bytes or model tokens.

For every error object:

```text
0 <= start <= end <= length(causal_explanation)
causal_explanation[start:end] == text
```

Error objects must be sorted by ascending `start`, then ascending `end`.
Overlapping spans are not permitted in schema version `0.1-draft`.

Most errors must use a non-empty span (`start < end`). An omission has no
surface text in the hallucinated explanation, so `EVID-OMIT` and `DX-OMIT`
may use a zero-length insertion span:

```text
start == end
text == ""
```

For an omission, `start` and `end` identify the point at which the missing
evidence or diagnosis should be restored, and `replacement` contains the text
to insert.

## Validity Rules

For a valid explanation:

```json
{
  "validity": "valid",
  "errors": [],
  "corrected_causal_explanation": "An unchanged copy of causal_explanation"
}
```

For an invalid explanation:

- `errors` must contain at least one object; and
- `corrected_causal_explanation` must contain the complete corrected
  explanation, not only the replacement spans.

## Formal-Run Input

Formal-run input records exclude:

- `validity`;
- `errors`; and
- `corrected_causal_explanation`.

## Submission Record

See [SUBMISSION_FORMAT.md](./SUBMISSION_FORMAT.md). Submission records use the
same `case_id`, validity values, error offsets, and hallucination codes defined
here.

## Version Compatibility

Participants should preserve `schema_version` in local processing. The
organizers should increment the version whenever field meanings, required
fields, offset conventions, or allowed labels change.
