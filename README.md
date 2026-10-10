# Financial Document Extraction with LLM Structured Outputs

> **Status: in progress.** The scope and approach below are the build plan. Results and run instructions will be added as each stage is completed.

A document-processing pipeline that extracts key fields from receipts and invoices into schema-validated JSON using an LLM, with an evaluation script that scores the output against labeled ground truth.

## Problem

Financial workflows (lending, expense processing, reconciliation) depend on pulling structured fields out of unstructured documents. Asking an LLM for JSON is easy; getting output that is reliably valid, complete, and measurably correct is the actual problem. This project focuses on that gap:

- enforcing a schema on LLM output instead of trusting free-form text
- handling malformed or missing fields explicitly
- measuring extraction quality instead of eyeballing it

## Dataset

A sample of receipts from the SROIE (ICDAR 2019) dataset, which provides labeled key fields (company, date, address, total). About 30-50 documents are used for evaluation. The dataset is not committed to this repository.

## Approach

1. **Schema**: define the target fields as a Pydantic model (types, required/optional fields, basic validators such as date and amount formats).
2. **Extraction**: call the Gemini API with structured output (response schema) so the model returns JSON matching the schema.
3. **Validation and retry**: validate every response against the Pydantic model; on failure or missing required fields, retry with the validation error fed back to the model, up to a fixed limit.
4. **API**: FastAPI endpoint that accepts a document upload and returns the validated JSON.
5. **Evaluation**: script that runs the pipeline over the labeled sample and compares each extracted field to ground truth after normalisation (case, whitespace, date and amount formats), reporting accuracy per field.

## Planned project structure

```
document-extraction-llm/
├── data/                  # sample receipts and labels (gitignored)
├── src/
│   ├── schema.py          # Pydantic models
│   ├── extractor.py       # Gemini call, validation, retry logic
│   └── normalize.py       # field normalisation for comparison
├── app/
│   └── main.py            # FastAPI upload endpoint
├── eval/
│   └── evaluate.py        # field-level scoring against labels
├── requirements.txt
└── README.md
```

## Planned API

`POST /extract` accepts a document file and returns the extracted fields as validated JSON, or a clear error if validation fails after retries.

## Roadmap

- [x] Define problem, dataset, and evaluation plan
- [ ] Pydantic schema for target fields
- [ ] Gemini structured-output extraction
- [ ] Validation and retry handling
- [ ] FastAPI upload endpoint
- [ ] Field normalisation and evaluation script
- [ ] Failure analysis of incorrect extractions
- [ ] Results and usage sections in this README

## Results

To be added after evaluation.

## Tech stack

Python, Gemini API, Pydantic, FastAPI
