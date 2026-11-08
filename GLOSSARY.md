# PDF Extraction Evaluation

This project compares how faithfully PDF extraction techniques recover information for later chunking and retrieval.

## Language

**Subproject 1: PDF structuring and timing**:
The experiment that converts complete source PDFs into structured outputs and records processing times. It does not judge the correctness or completeness of extracted content.

**Subproject 2: extraction quality evaluation**:
The later experiment that assesses the extracted content produced by Subproject 1 against manually checked references.

**Reference annotation**:
A manually checked record of information visible in a source PDF, used as the expected result for evaluating extraction. Its coverage is explicit: selected regions or a complete page.
_Avoid_: Ground truth when the content has not been manually checked

**Figure**:
A visual region such as a chart, diagram, or photograph that carries information. A figure can exist regardless of whether the PDF stores it as an embedded image or as vector drawing instructions.
_Avoid_: Image when referring only to an embedded image file

**Figure preservation**:
Recovery of a figure as a usable visual asset associated with its source page.

**Figure information extraction**:
Recovery of the facts expressed inside a figure, such as labels, values, relationships, and explanatory text.

**Extraction fidelity**:
The extent to which recovered content agrees with the source PDF, including its text, table structure, and figure information. Fidelity is evaluated separately for each content type.
