# PDF extraction benchmark: agreed subprojects

## Subproject 1: PDF structuring and timing

This is the current scope. Produce structured outputs and processing-time measurements before assessing extraction quality.

- Run PyMuPDF, Docling, and PaddleOCR PP-StructureV3 locally without paid APIs.
- Convert all three complete PDFs in [Sources/To_test](../Sources/To_test/), totaling 51 pages. Reference annotations are not prerequisites.
- Include OCR, layout extraction, table extraction, and figure asset saving where supported. Missing or unsupported output must be recorded explicitly.
- Save Markdown, structured JSON, figure assets, and a timing report for each PDF/tool combination. Retain source and page references wherever supported, so later evaluation can trace outputs back to the PDFs.
- Measure elapsed conversion time from opening the PDF through saving its structured outputs and figure assets. Report model initialization separately; exclude dependency installation and model downloads from conversion time.
- Perform three conversions per PDF/tool combination and report their median. Each repeat must perform the conversion rather than reuse saved extraction results.
- Retain individual timings and record tool versions and execution configuration so results can be understood and repeated.
- Produce outputs and timing measurements without an aggregate score or an overall winner.

Subproject 1 does not assess accuracy, extraction completeness, reading-order correctness, or readiness for chunking. Output counts and successful file creation are operational observations, not quality scores. Chart interpretation, generated figure descriptions, chunking, retrieval, and answer generation are outside this first pass.

Exact version pins, OCR settings, output organization, and execution controls will be made concrete during specification or implementation. They must be recorded with the results; no further product-scope decision is currently pending for Subproject 1.

## Subproject 2: extraction quality evaluation

This later subproject assesses the outputs produced by Subproject 1 using manually checked reference annotations. Text, tables, and figure preservation will be evaluated separately. Figure information extraction and its additional processing time can be investigated as a separately configured extension.

Annotation coverage and volume, precise metrics, normalization, target matching, and treatment of uncertainty remain decisions for Subproject 2. Its quality KPIs will be reported separately, without an aggregate score or an overall winner. These decisions do not block Subproject 1.

A [reference template and annotation guide](../evaluation/reference/README.md) are available for later use. No reference annotation is required at this stage.

## Interview handoff

The scope questions for Subproject 1 are complete, and the user selected `/to-spec`. The user confirmed the public-runner testing boundary in the [specification](./specs/subproject-1-pdf-structuring-and-timing.md). The specification is published as [GitHub issue #1](https://github.com/alvaro-zs/pdf-extraction-for-rag/issues/1) with the `ready-for-agent` label; the published body matches the local specification. No extractor or evaluator has been implemented or run.
