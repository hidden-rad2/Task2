# Hallucination Taxonomy

This document preserves the core Hidden-RAD2 Task 2 hallucination labels for
schema version `0.1-draft`.

## `ADD-PATH` — Unsupported Pathology

Adds a pathology, device, diagnosis, or finding that is not supported by the
available evidence.

## `ADD-DEVICE` — Unsupported Medical Device

Adds a medical device, tube, line, or related intervention that is not
supported by the available evidence.

## `ADD-REC` — Unsupported Recommendation

Adds an unsupported recommendation or follow-up action, such as an unwarranted
recommendation for CT or additional imaging.

## `LOC-LAT` — Location or Laterality Error

Changes the anatomical location, lung zone, or right-left orientation of a
finding.

## `SEV-CHG` — Severity Change

Incorrectly increases or decreases the severity of a finding.

## `NEG-FLIP` — Negation Reversal

Changes a present finding to absent or an absent finding to present.

## `CAUSE-WRONG` — Incorrect Causal Relationship

Connects a finding and impression through an unsupported causal relationship.

## `DDX-WRONG` — Incorrect Differential Diagnosis

Introduces a differential diagnosis that is contradicted or unsupported by
the available evidence.

## `EVID-OMIT` — Evidence Omission

Omits important evidence required to support a final impression.

## `DX-OMIT` — Diagnosis Omission

Omits a diagnosis or final impression that should be represented in the causal
explanation.

## `TERM-MINOR` — Minor Terminology Substitution

Replaces a clinical term with a related but inaccurate or insufficiently
specific term.

## `CONTRA` — Internal Contradiction

Introduces mutually inconsistent statements within the causal explanation.

## `FLUENCY` — Fluency or Grammatical Degradation

Damages grammar or expression while largely preserving the intended clinical
meaning.

## `CERT-FLIP` — Certainty Change

Changes the certainty of a claim, such as converting a tentative impression
into a definitive diagnosis or vice versa while otherwise preserving the
claim.

## Annotation Principles

- Assign a label based on the clinical effect of the error, not only its
  surface wording.
- Use the smallest span that captures the complete error without omitting text
  required to understand the error.
- Laterality and anatomical-location substitutions use `LOC-LAT` even when
  they also create an internal inconsistency.
- A valid explanation has no hallucination label.
- `EVID-OMIT` and `DX-OMIT` may use a zero-length insertion span because the
  missing text is not present in the hallucinated explanation.
- Corrections must resolve the labeled error without introducing a new claim
  unsupported by the available evidence.

Additional subtypes, precedence rules, and adjudicated boundary cases may be
added in a later schema version.
