# Hidden-RAD2 Task 2: Causal Verification and Correction

This repository provides the dataset specification, examples, and release
information for **Task 2: Causal Verification and Correction** of the
**NTCIR-19 Hidden-RAD2 challenge**.

Task 2 evaluates whether an AI system can verify a causal explanation for a
chest X-ray case, identify unsupported or incorrect claims, and produce a
safe, evidence-grounded correction.

> **Repository status:** The documentation and preliminary JSONL schema are available.
> Dataset files and final submission limits will be added
> after the radiologist review and release preparation are complete.

## Task Definition

Each case contains a causal exploration section to be verified. The
explanation may be valid or may contain one or more controlled hallucinations.

Participants must:

1. classify the explanation as valid or invalid;
2. identify the exact text span of each error;
3. assign an official hallucination type to each error;
4. submit an evidence-grounded corrected explanation; and
5. provide a confidence score from 0 to 1.

Participants must not assume that every explanation contains an error. The
dataset includes both valid explanations and explanations containing
controlled hallucinations.

## Data Provided for Each Case

Each released input record contains:

1. a causal exploration section to be verified;
2. a reference to the original radiology report in MIMIC-CXR; and
3. one or more references to the corresponding chest X-ray images in
   MIMIC-CXR.

The report and image files are **not redistributed** in this repository.
Participants must retrieve them directly from MIMIC-CXR using their own
authorized PhysioNet accounts.

Participants may use the report, the image, or both as supporting evidence.

## Evidence-Use Categories

Each submitted run must identify the evidence actually used during inference.

| Code | Category | Evidence used |
|---|---|---|
| `R` | Report-only | Causal explanation and original radiology report |
| `I` | Image-only | Causal explanation and chest X-ray image or images |
| `RI` | Report-and-Image | Causal explanation, original report, and image or images |

These categories describe the evidence used by a run. They are not separate
subtasks, and participants do not need to select a category in advance.

## Repository Layout

```text
.
├── README.md
├── DATA_CARD.md
├── SCHEMA.md
├── HALLUCINATION_TAXONOMY.md
├── SUBMISSION_FORMAT.md
├── CHANGELOG.md
├── data/
│   └── README.md
└── examples/
    ├── README.md
    ├── sample_input.jsonl
    ├── sample_labeled.jsonl
    └── sample_submission.jsonl
```

## JSONL Format

All dataset and submission files use
[JSON Lines](https://jsonlines.org/) (`.jsonl`):

- UTF-8 encoding;
- one JSON object per line;
- no outer JSON array; and
- one `case_id` per record.

JSONL is used because a case may contain multiple images and multiple error
annotations. See [SCHEMA.md](./SCHEMA.md) for the complete field definitions.

### Input record

```json
{
  "schema_version": "0.1-draft",
  "case_id": "HR2-T2-TEST-000001",
  "report_ref": {
    "source": "MIMIC-CXR",
    "report_id": "example-report-id",
    "url": "https://physionet.org/..."
  },
  "image_refs": [
    {
      "source": "MIMIC-CXR",
      "dicom_id": "example-dicom-id",
      "url": "https://physionet.org/..."
    }
  ],
  "causal_explanation": "The explanation to be verified."
}
```

### Labeled record

Training and development releases additionally contain gold labels:

```json
{
  "validity": "invalid",
  "errors": [
    {
      "error_id": "E1",
      "start": 76,
      "end": 102,
      "text": "right lower lobe pneumonia",
      "type": "LOC-LAT",
      "replacement": "left lower lobe pneumonia"
    }
  ],
  "corrected_causal_explanation": "The complete corrected explanation."
}
```

The example above shows only the gold-label fields. A labeled JSONL record also
contains all fields from the input record.

## Error-Span Convention

The draft schema uses:

- zero-based Unicode character offsets;
- an inclusive `start` index; and
- an exclusive `end` index.

For every error annotation, the following expression must be true:

```text
causal_explanation[start:end] == text
```

For a valid explanation, `validity` is `"valid"` and `errors` is an empty
array (`[]`). See [SCHEMA.md](./SCHEMA.md) for additional validation rules.

## Hallucination Types

| Code | Type |
|---|---|
| `ADD-PATH` | Unsupported Pathology |
| `ADD-DEVICE` | Unsupported Medical Device |
| `ADD-REC` | Unsupported Recommendation |
| `LOC-LAT` | Location or Laterality Error |
| `SEV-CHG` | Severity Change |
| `NEG-FLIP` | Negation Reversal |
| `CAUSE-WRONG` | Incorrect Causal Relationship |
| `DDX-WRONG` | Incorrect Differential Diagnosis |
| `EVID-OMIT` | Evidence Omission |
| `DX-OMIT` | Diagnosis Omission |
| `TERM-MINOR` | Minor Terminology Substitution |
| `CONTRA` | Internal Contradiction |
| `FLUENCY` | Fluency or Grammatical Degradation |
| `CERT-FLIP` | Certainty Change |

See [HALLUCINATION_TAXONOMY.md](./HALLUCINATION_TAXONOMY.md) for definitions.

The 14-type taxonomy is a draft based on the current task-design document. It
must be confirmed before the first official release.

## Dataset Construction

Each case begins with a draft causal explanation generated from a structured
chest X-ray interpretation and the corresponding medical report. A radiologist
reviews the explanation before it is adopted as the gold explanation.

Selected gold explanations are modified by inserting one or a small number of
controlled hallucinations. Valid explanations without inserted errors are also
included.

## Required Submission Output

Every submitted record contains:

- `case_id`;
- `validity`;
- `errors`;
- `corrected_causal_explanation`;
- `confidence`;
- `evidence_used`; and
- `run_id`.

See [SUBMISSION_FORMAT.md](./SUBMISSION_FORMAT.md) and
[examples/sample_submission.jsonl](./examples/sample_submission.jsonl).

## Dataset Release

- **July 22, 2026 — Task 2 dataset release (tentative)**
- **July 30, 2026 — Formal run begins**
- **August 17, 2026 — Run submission deadline**
- **Around August 22, 2026 — Evaluation results returned**
- **September 1, 2026 — Participant paper draft due**

The Task 2 dataset release is subject to completion of radiologist review. If
the release is delayed, the formal-run schedule may be adjusted accordingly.

All submission deadlines are 11:59 p.m. Anywhere on Earth (AoE), unless
otherwise specified.

## Evaluation

Submitted runs will be evaluated on:

- validity classification;
- error-span detection;
- hallucination-type classification;
- correction quality;
- clinical appropriateness;
- avoidance of new hallucinations; and
- confidence calibration.

Results may also be grouped by `R`, `I`, and `RI` evidence-use category.

## MIMIC-CXR Access

All Task 2 radiology reports and images are restricted MIMIC-CXR data. Every
team member who accesses or handles those files must obtain individual
authorization from PhysioNet.

- [MIMIC-CXR on PhysioNet](https://physionet.org/content/mimic-cxr/2.1.0/)
- [PhysioNet credentialing and training](https://physionet.org/about/citi-course/)
- [Hidden-RAD2 MIMIC-CXR access instructions](https://sites.google.com/view/hidden-rad2/tasks/task-2-definition/mimic-cxr-data-access)

MIMIC-CXR files, account credentials, and download access must not be shared.

## Important Notes

- The organizers distribute references to MIMIC-CXR resources, not the
  restricted report or image files.
- Every team member accessing MIMIC-CXR must have individual PhysioNet
  authorization.
- Participants may use the report only, the image only, or both.
- `R`, `I`, and `RI` are evidence-use categories, not separate subtasks.
- Participants must not assume that every explanation is invalid.
- A correction must not introduce new unsupported claims.
- Synthetic examples in this repository are not MIMIC-CXR records and must not
  be used for clinical purposes.

## Documentation

- [Dataset card](./DATA_CARD.md)
- [JSONL schema](./SCHEMA.md)
- [Hallucination taxonomy](./HALLUCINATION_TAXONOMY.md)
- [Submission format](./SUBMISSION_FORMAT.md)
- [Change log](./CHANGELOG.md)

## Challenge Information

- [Hidden-RAD2 website](https://sites.google.com/view/hidden-rad2/)
- [Task 2 definition](https://sites.google.com/view/hidden-rad2/tasks/task-2-definition)
- [Task 2 details](https://sites.google.com/view/hidden-rad2/tasks/task-2-definition/task-2-details)
- [Official schedule](https://sites.google.com/view/hidden-rad2/schedule)

## Citation

Citation information for Hidden-RAD2 and the NTCIR-19 task overview paper will
be added when available.
