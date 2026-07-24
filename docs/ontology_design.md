# NS4KGE Ontology — Design Rationale

A detailed account of the modeling choices behind the NS4KGE ontology. The poster
gives a condensed version; this document expands on *why* each concept exists and
how the schema mirrors the way negative-sampling-for-KGE experiments are reported
in the literature.

## 1. Design philosophy: use-driven modeling

The ontology follows a **use-driven (competency-question-driven) design**: every
modeling choice is traceable to one or more competency questions
([`competency_questions.md`](competency_questions.md)) and to the three analytical
use cases (literature navigation, evaluation coverage, configuration exploration).
We deliberately did **not** try to model the full KGE landscape — only what the
use cases require and what can be extracted reliably from articles.

Three principles guide the schema:

1. **Traceability.** Each concept is introduced to support a competency question,
   keeping an explicit link between modeling choices and intended usage.
2. **Extractability / pragmatic scoping.** The scope is restricted to concepts
   that can be reliably extracted from scientific articles and operationalized
   consistently across the corpus. Finer-grained notions considered during design
   (e.g. sampling procedures, method-level assumptions) were excluded because
   reliable extraction could not be achieved at corpus scale.
3. **Reporting practice, not algorithm semantics.** The ontology represents the
   *experimental structure and reporting practices* of the field, not the internal
   semantics of negative-sampling algorithms or KGE models.

## 2. Core concepts

**Article** is the main entry point and represents a scientific publication. It is
aligned with `bibo:AcademicArticle` (see §4). Each article links to one or more
**Experimentation** instances, which group the evaluation setups described in the
paper.

**Configuration is the central concept.** It represents a *single reported
experimental result*, defined by a `Dataset`, a `Task`, a `Metric`, a `KGEModel`,
and an `NSMethod`, together with the associated numerical `result`. This mirrors
the structure of experimental result tables in KGE papers, where **each row is a
distinct configuration**. Modeling a configuration as a first-class object is what
makes the field's tables queryable across articles.

**TrainingProtocol** captures training parameters common to a group of experiments
— `optimizer`, `lossFunction`, `hardware`, `learningRate`, `nsRatio`. This reflects
the structure of experimental sections, where training settings are typically
described once and reused across many reported results. Attaching them at the
`Experimentation` level (rather than repeating them on every `Configuration`)
avoids redundancy and matches how papers actually report.

**Proposed vs. used.** The ontology distinguishes methods/models *proposed as a
contribution* from those *used in a reported result*:

- `proposesNSMethod` / `proposesKGEModel` — the contribution of the paper.
- `hasNSMethod` / `hasKGEModel` — what a specific `Configuration` actually evaluates.
- `mentionsNSMethod` / `mentionsKGEModel` — cited in prose (e.g. related work) without necessarily being evaluated.

This lets the graph answer *"which methods are actually compared in this article?"*
versus *"which are merely cited?"* — a distinction impossible to recover from raw
text, and one the validation confirms the KG captures in both directions.

## 3. Categories, and room to grow

`NSMethod` instances can be organized with a **Category** and **Subcategory**
derived from the taxonomy of Madushanka & Ichise. These categories are also reused
as a **controlled vocabulary during extraction**, to reduce inconsistent or
hallucinated assignments. They are **made available and are partially populated**
in the current knowledge graph (e.g. `adversarial-ns`, `dynamic-ns`, `mix-up-ns`,
`self-adversarial-ns`, `static-ns`, `text-based-ns`); they can be populated more
fully as the taxonomy coverage is extended. The same categorization approach could
naturally be applied to `KGEModel` in a future iteration.

## 4. Incomplete reporting is first-class

Missing values are represented by the **absence of the corresponding property**
rather than by a null placeholder. As a result, incomplete reporting patterns
remain explicitly queryable — "which articles do not report the learning rate?"
is answered directly. This is a deliberate choice: the resource is meant to study
reporting gaps, so gaps must be visible, not hidden.

## 5. Reuse of existing vocabularies

To improve interoperability, the ontology reuses selected Semantic Web vocabularies:

- `ns4kge:Article ⊑ bibo:AcademicArticle` — interoperability with bibliographic
  knowledge graphs (DBLP, Semantic Scholar…).
- `ns4kge:Dataset ⊑ void:Dataset` — lets dataset metadata be combined with
  experimental configurations.

Other classes are intentionally kept domain-specific. Generic ML ontologies such as
ML-Schema define related concepts, but additional alignments would add
terminological complexity without a clear analytical benefit for this use case.

## 6. OWL inference for convenient querying

The ontology is OWL 2, authored in Protégé, and published (see the repository
README for the `w3id` IRI and Zenodo DOI). Beyond the T-Box, it declares
inference constructs that shorten common query paths:

- **Inverse properties** for every structural relation
  (`isExperimentationOf`, `isConfigurationOf`, `isDatasetOfConfiguration`, …),
  so the graph can be traversed in either direction.
- **Property chains** that materialize otherwise multi-hop paths:
  - `hasArticleConfiguration` = `hasExperimentation ∘ hasConfiguration` — links an
    article directly to the configurations it reports.
  - `usesDataset` = `hasExperimentation ∘ hasConfiguration ∘ hasDataset` — links an
    article directly to the datasets used in its configurations.

  Under an OWL 2 RL reasoner these let a query go straight from an `Article` to its
  configurations or datasets without spelling out the full path, which keeps the
  competency-question queries concise.

See [`methodology.md`](methodology.md) for how the ontology and the extraction
pipeline were refined jointly.
