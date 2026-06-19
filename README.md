# NS4KGE Ontology

Reusable ontology for representing scientific papers about knowledge graph embeddings and negative sampling.

## Contents
- `ontology/ns4kge_ontology.ttl` is the authoritative OWL/Turtle ontology.
- `ontology/ns4kge_shapes.ttl` is generated SHACL from ontology domains, ranges, and cardinality restrictions.
- `docs/competency_questions.md` lists the competency questions used by the paper.
- `examples/minimal_instance.ttl` is a synthetic example instance; it is not derived from any published paper.

## Version
- Current artifact version: `v1.0.1`.

## Persistent Identifiers
- Ontology IRI: `https://w3id.org/ns4kge/ontology`.
- Ontology term namespace: `https://w3id.org/ns4kge/ontology#`.
- Version IRI: `https://w3id.org/ns4kge/ontology/1.0.1`.
- Class example: `https://w3id.org/ns4kge/ontology#Article`.
- Property example: `https://w3id.org/ns4kge/ontology#hasConfiguration`.

## Resource Availability
- Repository: `https://github.com/IlyesTebourski/ns4kge-ontology`.
- Release: `v1.0.1`.
- Release date: 2026-05-13.
- License: CC BY 4.0.
- Zenodo DOI: `https://doi.org/10.5281/zenodo.20160705`.
- Dereferenceable ontology IRI: `https://w3id.org/ns4kge/ontology`.
- Documentation (WIDOCO): `https://ilyestebourski.github.io/ns4kge-ontology/widoco/index-en.html`.

The repository contains only the ontology, generated SHACL shapes, documentation, and synthetic examples. It does not redistribute source article text.

## Schema Diagram

![NS4KGE ontology schema](docs/ns4kge-schema.svg)

## Core Model
- `Article` describes a scientific article.
- `NSMethod` describes negative sampling methods proposed, mentioned, or evaluated in configurations.
- `KGEModel` describes knowledge graph embedding models.
- `Experimentation` connects an article to one `TrainingProtocol` and one or more `Configuration` instances.
- `Configuration` captures `Task`, `Dataset`, `Metric`, optional `KGEModel`, optional `NSMethod`, and a numeric `result`.

## Modeling Choices
- Articles are modeled as `ns4kge:Article` and subclass `bibo:AcademicArticle` to reuse an established bibliographic type.
- Experimental evidence is separated from bibliographic metadata through `ns4kge:Experimentation`.
- `ns4kge:Configuration` represents one reported result for a task, dataset, metric, KGE model, and optional negative sampling method.
- Negative sampling categories and subcategories are attached to `ns4kge:NSMethod`, not directly to articles.
- Benchmark datasets are modeled as `ns4kge:Dataset` and subclass `void:Dataset`.
- The ontology uses `dcterms` for artifact metadata, `vann` for preferred namespace metadata, BIBO for article alignment, and VoID for dataset alignment.

## Sustainability And Maintenance
- Current release is `v1.0.1`.
- Ontology term IRIs are intended to remain stable under `https://w3id.org/ns4kge/ontology#`.
- Breaking ontology changes should use a new version IRI and release tag.
- SHACL shapes are generated from ontology domains, ranges, and cardinality restrictions using the companion extraction pipeline.

## Limitations
- The ontology focuses on negative sampling for knowledge graph embeddings and is not a general ontology of all machine-learning experiments.
- SHACL shapes reflect the current modeling constraints and should not be interpreted as complete semantic validation of extracted scientific claims.
- The populated knowledge graph is produced by a separate LLM-assisted extraction pipeline, so extraction errors and omissions are outside this ontology artifact.
- Source article text is excluded for copyright reasons.

## Tooling
Python tooling lives in the separate `ns4kge-extraction-pipeline` artifact. From that folder, regenerate shapes with:

```bash
uv run nofacts-gen-shapes --ontology ../ns4kge-ontology/ontology/ns4kge_ontology.ttl --out ../ns4kge-ontology/ontology/ns4kge_shapes.ttl
```

Validate a populated KG with:

```bash
uv run nofacts-validate --data ../ns4kge-kg/kg/ns4kge_populated.ttl --shapes ../ns4kge-ontology/ontology/ns4kge_shapes.ttl
```
## Comparison

Positioning of NS4KGE with respect to related resources.
✓ supported · △ partial/limited · ✗ not supported · N/A not applicable (e.g. schema-only).
*Bib. KGs* groups Semantic Scholar, OpenAlex, and DBLP.

| Feature | ORKG | CS-KG | Bib. KGs | ML-Schema | OntoDM | NS4KGE |
|---|:--:|:--:|:--:|:--:|:--:|:--:|
| Bibliographic metadata (papers, authors, venues) | ✓ | ✓ | ✓ | ✗ | ✗ | ✓ |
| Structured research contributions (tasks, methods, datasets) | ✓ | ✓ | ✗ | ✗ | ✗ | ✓ |
| Machine learning / experiment modeling | △ | △ | ✗ | ✓ | ✓ | ✓ |
| Literature-populated resource | ✓ | ✓ | ✓ | N/A | N/A | ✓ |
| Automated population from scientific articles | △ | ✓ | ✓ | N/A | N/A | ✓ |
| Reuse of standard vocabularies / interoperability | ✓ | ✓ | △ | ✓ | ✓ | ✓ |
| Configuration-level experimental results (dataset–model–method–metric–score) | △ | ✗ | ✗ | ✓ | △ | ✓ |
| KGE- and negative-sampling-specific concepts | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ |
| Distinction between proposed methods and baselines | △ | ✗ | ✗ | ✗ | ✗ | ✓ |
| Cross-paper comparison of experimental practices | △ | △ | ✗ | ✗ | ✗ | ✓ |
| Designed for analyzing negative sampling in KGE literature | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ |

NS4KGE is complementary to existing resources. Bibliographic knowledge graphs (Semantic Scholar, OpenAlex, DBLP) provide rich paper, author, and venue metadata but do not represent research contributions or experimental content. ORKG and CS-KG add structured research contributions (tasks, methods, datasets, metrics), and CS-KG, like NS4KGE, is populated automatically from a large corpus; however, neither models experimental configurations linking a sampling method, an embedding model, a dataset, a metric, and a reported score, nor distinguishes proposed methods from baselines. ML-Schema and OntoDM provide reusable vocabularies for describing machine learning and data mining experiments at the level of runs, evaluations, and measures, but are general-purpose schemas rather than populated literature resources, and are not designed for cross-paper comparison of reported practices. NS4KGE reuses standard Semantic Web vocabularies for interoperability while adding KGE- and negative-sampling-specific concepts, configuration-level results extracted from scientific articles, and support for cross-paper analysis of negative sampling practices.

## License
Ontology files, documentation, and examples are licensed under CC BY 4.0.
