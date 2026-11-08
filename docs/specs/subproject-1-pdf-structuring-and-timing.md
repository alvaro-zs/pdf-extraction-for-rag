# Subproject 1: PDF structuring and timing

## Problem Statement

The researcher wants to compare PyMuPDF, Docling, and PaddleOCR PP-StructureV3 on the same complete PDFs before deciding how to evaluate their extracted information for later RAG work. There is currently no extraction runner, reproducible timing procedure, saved structured output collection, or comparison report in this repository.

The selected corpus contains three PDFs totaling 51 pages, including Portuguese prose, numeric tables, charts, vector graphics, and sideways content. Initial inspection found no usable native text layer. A native-text-only extraction would therefore be an inadequate configuration for this experiment.

The immediate need is to obtain inspectable outputs and processing times. Manually checked reference annotations are not yet available and must not block these conversions. Successful processing and output counts must not be presented as measured extraction quality.

## Solution

Build a local benchmark runner that converts every selected PDF with all three tools, saves each tool's structured outputs and figure assets, and produces individual and median processing-time reports. Run three complete conversions for each PDF/tool combination, yielding 27 measured conversions for the current corpus.

Include OCR, layout processing, table extraction, and figure asset saving where supported. Save Markdown, structured JSON, and figure assets with source/page provenance wherever the tool exposes it. Report missing or unsupported capabilities explicitly.

Measure PDF conversion through output serialization, separately from initialization, installation, and model downloads. Preserve outputs, timing records, and configuration details for a later Subproject 2: extraction quality evaluation. Do not produce accuracy scores, readiness-for-chunking judgments, an aggregate score, or an overall winner.

## User Stories

1. As a researcher, I want to process all three selected complete PDFs, so that I can inspect whole-document outputs before preparing reference annotations.
2. As a researcher, I want to compare PyMuPDF, Docling, and PaddleOCR PP-StructureV3 on identical inputs, so that the input corpus does not explain differences in runtime.
3. As a researcher, I want processing to run locally without paid APIs, so that this experiment does not depend on usage-based service charges.
4. As a researcher, I want dependency and model preparation separated from measured conversion, so that download time does not distort conversion measurements.
5. As a researcher, I want OCR-capable configurations, so that PDFs without native text can still produce usable structured output.
6. As a researcher, I want Portuguese-aware OCR settings where supported, so that the configuration reflects the source documents' language.
7. As a researcher, I want layout and table processing enabled where supported, so that the saved output includes the structure each tool can recover.
8. As a researcher, I want detected figure regions saved as visual assets, so that I can inspect them and evaluate figure preservation later.
9. As a researcher, I want vector figure regions to be eligible for saving when supported, so that the experiment does not treat embedded bitmaps as the only possible figures.
10. As a researcher, I want Markdown per document and tool, so that I can quickly read and compare the resulting representations.
11. As a researcher, I want structured JSON per document and tool, so that later work can examine extracted structure programmatically.
12. As a researcher, I want the tool-produced structure retained, so that a common report does not hide differences between extractors.
13. As a researcher, I want source identifiers and page provenance wherever available, so that later reference annotations can be matched to extracted content.
14. As a researcher, I want unavailable metadata and unsupported exports recorded explicitly, so that absent information is not mistaken for a successful extraction.
15. As a researcher, I want conversion time to include opening the PDF, processing every page, and saving requested outputs, so that it represents the complete conversion task.
16. As a researcher, I want initialization reported separately, so that I can distinguish startup overhead from conversion time.
17. As a researcher, I want three fresh conversions per PDF/tool combination, so that I can see runtime variation rather than rely on one observation.
18. As a researcher, I want the median reported alongside individual timings, so that the summary remains traceable to the measured runs.
19. As a researcher, I want each repetition's outputs preserved separately, so that later runs do not overwrite evidence from earlier ones.
20. As a researcher, I want elapsed time per PDF and seconds per page, so that I can inspect document-level and page-normalized processing costs.
21. As a researcher, I want tool versions, model identifiers, OCR settings, and execution configuration recorded, so that I can repeat and interpret the experiment.
22. As a researcher, I want runs performed sequentially under a documented device policy, so that competing conversions do not distort the measurements.
23. As a researcher, I want failures and timeouts recorded per run, so that missing results are visible in the comparison.
24. As a researcher, I want other PDF/tool combinations to continue after an isolated failure, so that one failure does not erase the rest of the experiment.
25. As a researcher, I want incomplete combinations distinguished from combinations with three successful runs, so that a partial result cannot look like a complete median measurement.
26. As a researcher, I want a readable summary and machine-readable timing records, so that I can inspect the results directly and analyze them later.
27. As a researcher, I want no quality reference requirement in this subproject, so that I can obtain outputs and timings immediately after setup.
28. As a researcher, I want chart interpretation and generated figure descriptions kept outside this first pass, so that their additional processing is not mixed into core structuring time.
29. As a researcher, I want the source PDFs preserved, so that every tool and later evaluation uses the same original evidence.
30. As a researcher, I want this output collection to remain available to Subproject 2, so that quality assessment can reuse the measured conversions.

## Implementation Decisions

- **Application boundary:** provide one public benchmark-runner entry point with a thin command-line interface. It accepts the input selection, output destination, selected tools, repetition count, and explicit execution settings. Defaults cover the agreed three-tool, three-repetition experiment; selecting a subset is useful for setup checks.
- **Modules:** separate benchmark orchestration, tool adapters, and artifact/report writing. Adapters cover PyMuPDF, Docling, and PaddleOCR PP-StructureV3. They expose the same operational contract: initialize, convert a complete PDF, export supported artifacts, and report status and provenance. Do not force their extracted content into a lossy common table or document schema.
- **Repository baseline:** no extraction implementation, test framework, or prior benchmark seam exists. Keep the new command and configuration small enough for this experiment rather than introducing a service, scheduler, or web application.
- **Input selection:** default to the existing three selected PDFs, without importing the larger reference collection. Discover PDF inputs deterministically and exclude text files and prior outputs. Record a source identifier, content hash, and physical page count. Do not modify the originals.
- **Dependency preparation:** use isolated, documented Python environments with recorded dependency versions. Separate environments per tool are acceptable if their dependencies conflict. Acquire required model and OCR-language resources before measured conversions. Source PDFs must remain local; paid APIs and remote document inference are disabled.
- **OCR and layout configuration:** use the tools' OCR-capable structured conversion paths, including the structured API available in the installed PyMuPDF generation. Pin the actual OCR backend, language settings, rendering resolution, model identifiers, table/layout options, and orientation options used. Prefer Portuguese support, with English also enabled when appropriate. Record unsupported controls rather than claiming identical capabilities across tools.
- **First-pass features:** enable OCR, layout, table extraction, and figure asset saving where supported. Disable optional generative figure descriptions and chart interpretation. A visual asset may be a rendered detected figure region; embedded-image extraction alone does not establish coverage of vector figures.
- **Output contract:** retain tool-native structured JSON, document Markdown, and figure assets for each PDF/tool/repetition. A stable manifest associates them with the source document, repetition, tool, configuration, page provenance where available, and capability/status information. If an export or field is unsupported, record that status and reason; do not fabricate content or page associations. Preserve native page results where document-level export requires aggregation.
- **Timing boundary:** use a monotonic elapsed-time clock. Start immediately before opening the PDF for the conversion and stop after the requested conversion artifacts have been serialized and closed. Include rendering, OCR, layout/table work, supported figure saving, and any export aggregation. Exclude report summarization, dependency installation, model acquisition, and separately measured engine initialization.
- **Initialization:** report process/library/model initialization separately with a documented boundary. Resolve lazy model initialization before measured conversions where possible. Any required initialization probe is recorded as excluded setup and must not pre-extract an evaluated PDF. If a tool cannot separate initialization cleanly, report that limitation explicitly rather than silently excluding or reclassifying work.
- **Repetitions:** execute three conversions per PDF/tool combination with a ready engine. Reopen and reconvert the source each time, without reusing converted document results. Model reuse is allowed and documented. These are warm-engine measurements; operating-system file caches need not be cleared or described as cold-start performance.
- **Execution controls:** run conversions sequentially on the same machine under an explicit, recorded device policy. Record device type, operating-system/Python details, worker/thread settings, tool/model versions, and effective extraction settings. Do not silently mix CPU and GPU results in one comparison configuration. Run limits or timeouts are configurable and recorded.
- **Run records:** retain document identity, tool/configuration identity, repetition number, elapsed conversion time, initialization association, source page count, seconds per page, status, artifact locations, warnings, and failure details. Diagnostic artifact/page counts may be retained but are not quality KPIs.
- **Summary rules:** show all individual run statuses and timings. Publish the official median conversion time and median seconds per page only when all three required runs succeed. Otherwise mark the combination incomplete and preserve observed timings without presenting a full three-run median. Include successful/attempted counts. Do not rank tools or declare an overall winner.
- **Failure handling:** isolate an individual conversion failure or timeout, retain its elapsed time and diagnostic information, and continue independent combinations. Clearly distinguish a failed conversion from an unsupported export and from completed processing with warnings. Fatal setup errors must identify the affected tool and leave a usable partial report when other tools can run.
- **Preservation:** keep each benchmark invocation and repetition distinct so that reruns do not overwrite prior artifacts. Record enough manifest information for Subproject 2 to locate and assess the exact outputs that were timed.
- **Operational acceptance:** after automated checks, execute the full agreed corpus against all three tools for three repetitions. Deliver the resulting artifacts and report actual successes and failures. If a tool cannot run in the available environment, record the concrete blocker and partial results; do not claim the full experiment completed or invent measurements.

## Testing Decisions

- **Confirmed primary seam:** test the public benchmark-runner boundary through its observable artifacts, statuses, and reports. Keep CLI wiring thin; do not create a separate test surface for every internal helper or extractor adapter. The user confirmed this testing approach before issue publication.
- Good tests assert external behavior and the agreed timing/output contract. They do not mirror private methods, assert incidental library internals, compare OCR wording, or impose absolute speed thresholds.
- Exercise orchestration and artifact/report writing through the same public runner using controlled extractor and clock dependencies. This permits deterministic checks without model downloads or lengthy conversions in the normal test suite.
- Verify requested inputs/tools/repetitions, output separation, source/configuration associations, supported versus unsupported artifacts, and retention of individual measurements.
- Verify that conversion timing encloses PDF opening, conversion, and requested artifact serialization while initialization and later report generation remain separate. Verify median calculation for three known timings and suppression of the official median after an incomplete set.
- Verify failure/timeout reporting, continuation of unaffected combinations, non-overwriting reruns, and operation with no reference annotations. Parsing a saved JSON artifact and resolving asset references are operational checks, not extraction-fidelity checks.
- Run small real-tool smoke conversions through the same runner using self-contained native-text and image-only PDF fixtures. Check that adapters initialize, process the requested pages, and produce parseable supported exports and explicit capability statuses. Keep these integration checks separate from the lightweight suite because they require installed engines and model resources.
- Prior art: there are no existing extraction tests, test fixtures, or test framework in this repository to extend. Prefer a small conventional Python test setup over bespoke infrastructure.
- Treat the actual 27-conversion experiment as the operational acceptance run, not as a repeatedly executed unit test. Report its measured outputs and limitations without claiming content correctness.

## Out of Scope

- Subproject 2: extraction quality evaluation, including reference annotation completion, accuracy metrics, extraction completeness, reading-order correctness, and semantic correctness of table/figure contents.
- Chart interpretation, chart-to-table reasoning, generated figure descriptions, and other optional semantic enrichment in this first pass.
- Chunk construction, embeddings, vector databases, retrieval, RAG answer generation, and end-to-end readiness-for-chunking judgments.
- Aggregate quality/performance scores, automatic tool selection, or an overall winner.
- Paid APIs, uploading PDFs for remote inference, or introducing a cloud execution service.
- Expanding the input corpus beyond the three selected PDFs by default.
- Systematic model tuning, exhaustive configuration sweeps, cross-hardware benchmarking, memory/energy profiling, or production deployment.
- Requiring identical extracted structure, identical OCR engines, or feature parity between libraries as a condition for running the comparison.

## Further Notes

- The selected PDFs contain 19, 10, and 22 physical pages respectively. All 51 pages are in scope for conversion; no six-page annotation pilot is required for Subproject 1.
- Initial inspection suggests outlined/vectorized lettering and confirms the need for an OCR-capable configuration. This observation does not establish extraction quality or guarantee a particular OCR result.
- Reference annotation materials already exist for later use. Their absence or draft status must not block this subproject.
- Local operation avoids paid API usage; model/dependency downloads and local compute remain necessary resources. Record the actual tools and model resources selected.
- Scope and user-facing outputs were agreed during the design interview. This specification does not authorize beginning implementation by itself; the current requested deliverable is the specification and its project issue.
- Official API behavior and dependencies should be checked against the versions pinned during implementation. The final run report must describe the configuration actually executed rather than relying on mutable library defaults.
