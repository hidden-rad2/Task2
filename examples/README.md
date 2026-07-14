# Examples

This directory contains synthetic examples demonstrating the Task 2 JSONL
formats. These examples are provided for format illustration only and are not
part of the official Task 2 dataset.

## Files

- `sample_input.jsonl`: Example input distributed to participants.
- `sample_labeled.jsonl`: Example input with reference annotations, provided
  only to illustrate the complete annotation structure.
- `sample_submission.jsonl`: Example participant submission.

## Important Notes

- All examples in this directory are synthetic.
- Each line must contain exactly one valid JSON object.
- Files must be encoded in UTF-8.
- The `case_id` values must match those in the corresponding input file.
- Refer to [`../SCHEMA.md`](../SCHEMA.md) for field definitions.
- Refer to [`../SUBMISSION_FORMAT.md`](../SUBMISSION_FORMAT.md) for submission
  requirements.
- Refer to [`../HALLUCINATION_TAXONOMY.md`](../HALLUCINATION_TAXONOMY.md) for
  error-type definitions.
