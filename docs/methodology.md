# NS4KGE — Iterative Construction Methodology

How the NS4KGE resource was built: an iterative loop in which **ontology design and
extraction are refined jointly** until population quality is stable, followed by a
four-stage population pipeline. This document records the methodology so the
resource is reproducible and extensible.

## 1. The iterative loop

Ontology construction and pipeline development are **not** sequential. They co-evolve:

1. **Competency questions** define what the ontology must answer, and therefore its
   scope (the T-Box).
2. The **population pipeline** attempts to instantiate each class from the corpus.
3. For each class, we **assess pipeline reliability** — can this concept be
   extracted consistently at corpus scale?
4. If a class is **not reliably extractable**, we act on it: *refine* the modeling
   and prompts, or *simplify / remove* the class. Concepts that cannot be
   operationalized consistently are dropped rather than populated unreliably.
5. Steps 2–4 repeat until population quality is stable, at which point the graph is
   populated in full and validated.

This is the **pragmatic scoping** principle in action: only concepts that survive
the reliability check are systematically populated. It is why the ontology stays
close to what articles actually report.

## 2. Population pipeline

Once the schema is stable, articles are turned into RDF/Turtle conforming to the
ontology through four stages.

### 2.1 Pre-processing
Articles are converted from PDF to Markdown with LlamaParse, preserving section
headers and result tables. The Markdown is then split into two streams: a **body
stream** (running text) and a **table stream** (extracted tables only), so textual
descriptions and experimental results can be processed independently. Long-context
models (GPT-5.2, Claude Sonnet 4) are used for document processing and table
extraction.

### 2.2 Extraction
Two complementary prompts:

- **Prompt 1 (body stream)** extracts the main conceptual entities — datasets, KGE
  models, NS methods, metrics, tasks, training components. Numerical results are
  excluded at this stage.
- **Prompt 2 (table stream)** extracts experimental **configurations** with their
  reported performances.

NS methods and KGE models found in the body are **injected into the second prompt**
to improve consistency across result tables. To limit graph size while preserving
representative coverage, extraction retains the top reported configurations plus at
least one configuration for each identified `(KGEModel, NSMethod)` pair. The two
outputs are merged into a unified JSON, normalized, and deterministically converted
to `.ttl`.

The prompts were **iteratively refined on a calibration subset of five articles**
until no structural inconsistencies remained; once stabilized they were frozen and
applied uniformly to the rest of the corpus.

### 2.3 Entity normalization
Rather than relying on external reference files, normalization combines
prompt-level canonicalization with lightweight post-processing. Shared entities
(datasets, KGE models, metrics, optimizers, loss functions) receive canonical
`snake_case` identifiers (e.g. `dataset_fb15k237`, `model_transe`), so surface-form
variants (`FB15k-237` vs `FB15K237`) collapse to one node. Article-specific entities
are prefixed with a per-article slug derived from the PDF filename to prevent
identifier collisions when merging articles into a single graph.

### 2.4 Validation
Generated RDF is checked with **SHACL constraints** enforcing the ontology's minimal
structural requirements — e.g. each `Configuration` must have at least one dataset,
one metric, and one numerical result. Beyond SHACL, extraction reliability is
quantified per entity type (precision / relative recall); see the validation reports
in the `ns4kge-kg` repository.

## 3. Corpus

The corpus consists of **55 scientific articles** on negative sampling for KGE,
spanning **2013–2025**. Inclusion required that an article (i) proposes or evaluates
at least one NS method in the KGE context, and (ii) reports experimental results
detailed enough to instantiate at least one `Configuration`. The corpus covers the
main NS categories identified in the survey of Madushanka & Ichise, with additional
recent articles added manually. The full article list is available in the knowledge
graph repository.

## 4. Resulting resource

The populated knowledge graph contains **53,057 RDF triples**, including **178
NSMethod**, **196 KGEModel**, **73 Dataset**, **18 Metric**, and **6,567
Configuration** instances, covering four evaluation tasks. Missing fields are
preserved as property absences, so incomplete reporting patterns remain queryable.

## 5. Reproducibility and extensibility

The extraction pipeline, prompts, and configuration details are public (see the
`ns4kge-extraction-pipeline` repository). New articles can be added **without
modifying the ontology schema**, provided they stay within the scope defined by the
competency questions — a direct benefit of the use-driven design described in
[`ontology_design.md`](ontology_design.md).
