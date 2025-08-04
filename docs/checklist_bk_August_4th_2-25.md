<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Based on this list of tickets, please create a detailed checklist of tiny tasks per ticket with checkboxes and unique IDs that consist of ticket IDs and tasks IDs for software development team to complete. Developing of each functionality should start from development of unit tests for it. Developing of each functionality should end with conducting of unit tests for it.

Below is a granular, test-driven checklist for every ticket.

- Each tiny task is prefixed with the parent ticket ID plus a two-digit task number (e.g., AIM2-002-01).
- All items use GitHub-style checkboxes.
- The first task under every ticket is “Write failing unit tests”, and the final task is “Run \& pass unit tests”.


### AIM2-001  Project skeleton \& packaging

- [ ] AIM2-001-01  Write failing unit tests for empty package import (`import aim2`)
- [ ] AIM2-001-02  Generate directory tree (`aim2/`, `tests/`, `data/`)
- [ ] AIM2-001-03  Create `pyproject.toml` with core dependencies
- [ ] AIM2-001-04  Add `ruff`, `pytest`, basic CI YAML
- [ ] AIM2-001-05  Implement `aim2.__init__` with semantic versioning
- [ ] AIM2-001-06  Run \& pass unit tests


### AIM2-002  Ingest loader – ChemOnt

- [ ] AIM2-002-01  Write failing unit tests: expected file path, ontology triple count >0
- [ ] AIM2-002-02  Implement CLI arg parsing (`--out`, `--url`)
- [ ] AIM2-002-03  Download latest ChemOnt OWL
- [ ] AIM2-002-04  Parse with Owlready2; validate root class
- [ ] AIM2-002-05  Serialise to `data/raw/chemont.owl`
- [ ] AIM2-002-06  Run \& pass unit tests


### AIM2-003  Ingest loader – NP-Classifier

- [ ] AIM2-003-01  Write failing unit tests: JSON → OWL individuals count
- [ ] AIM2-003-02  CLI scaffolding
- [ ] AIM2-003-03  Fetch latest NP-Classifier JSON
- [ ] AIM2-003-04  Transform to OWL classes
- [ ] AIM2-003-05  Save as `data/raw/np_classifier.owl`
- [ ] AIM2-003-06  Run \& pass unit tests


### AIM2-004  Ingest loader – Plant Metabolic Network

- [ ] AIM2-004-01  Write failing unit tests: table -> OWL compound individuals
- [ ] AIM2-004-02  CLI scaffolding
- [ ] AIM2-004-03  Download PMN CSV/TSV
- [ ] AIM2-004-04  Convert rows to OWL individuals w/ identifiers
- [ ] AIM2-004-05  Persist `pmn.owl`
- [ ] AIM2-004-06  Run \& pass unit tests


### AIM2-005  Ingest loader – Plant Ontology

- [ ] AIM2-005-01  Write failing unit tests: OBO term “leaf” exists
- [ ] AIM2-005-02  CLI scaffolding
- [ ] AIM2-005-03  Fetch PO OBO
- [ ] AIM2-005-04  Convert to OWL classes
- [ ] AIM2-005-05  Save as `po.owl`
- [ ] AIM2-005-06  Run \& pass unit tests


### AIM2-006  Ingest loader – NCBI Taxonomy

- [ ] AIM2-006-01  Write failing unit tests: filter retains only Viridiplantae
- [ ] AIM2-006-02  Download taxonomy dump
- [ ] AIM2-006-03  Filter plant lineage; output TSV
- [ ] AIM2-006-04  Index by TaxID
- [ ] AIM2-006-05  Run \& pass unit tests


### AIM2-007  Ingest loader – PECO \& GO

- [ ] AIM2-007-01  Write failing unit tests: class counts PECO>0, GO>0
- [ ] AIM2-007-02  Fetch PECO OBO; convert to OWL
- [ ] AIM2-007-03  Fetch GO slim; convert to OWL
- [ ] AIM2-007-04  Save both OWLs
- [ ] AIM2-007-05  Run \& pass unit tests


### AIM2-008  Ontology merge engine

- [ ] AIM2-008-01  Write failing unit tests: merged file contains all root IRIs
- [ ] AIM2-008-02  Design namespace collision rules
- [ ] AIM2-008-03  Implement merge routine
- [ ] AIM2-008-04  CLI (`--inputs`, `--output`)
- [ ] AIM2-008-05  Run \& pass unit tests


### AIM2-009  Ontology pruning utility

- [ ] AIM2-009-01  Write failing unit tests: prune to <=target classes
- [ ] AIM2-009-02  Define rule-set (keep evidence, remove orphan)
- [ ] AIM2-009-03  Implement pruning logic
- [ ] AIM2-009-04  CLI flags (`--target-count`)
- [ ] AIM2-009-05  Run \& pass unit tests


### AIM2-010  Add AIM2 object properties

- [ ] AIM2-010-01  Write failing unit tests: properties `affects`, `accumulates_in` exist
- [ ] AIM2-010-02  Declare properties \& hierarchies
- [ ] AIM2-010-03  Inject into ontology
- [ ] AIM2-010-04  Run \& pass unit tests


### AIM2-011  Ontology exporters

- [ ] AIM2-011-01  Write failing unit tests: OWL file loads, CSV row count >0
- [ ] AIM2-011-02  Implement `to_owl`
- [ ] AIM2-011-03  Implement `to_csv`
- [ ] AIM2-011-04  Run \& pass unit tests


### AIM2-012  Corpus builder MVP

- [ ] AIM2-012-01  Write failing unit tests: given query, parquet rows >0
- [ ] AIM2-012-02  Implement PubMed API fetch
- [ ] AIM2-012-03  Handle OA XML vs abstract
- [ ] AIM2-012-04  Integrate GROBID PDF → text
- [ ] AIM2-012-05  Persist parquet + metadata
- [ ] AIM2-012-06  Run \& pass unit tests


### AIM2-013  Corpus incremental update

- [ ] AIM2-013-01  Write failing unit tests: rerun adds zero duplicates
- [ ] AIM2-013-02  Checksum/PMID cache design
- [ ] AIM2-013-03  Implement incremental logic
- [ ] AIM2-013-04  Run \& pass unit tests


### AIM2-014  NER wrapper – Transformers

- [ ] AIM2-014-01  Write failing unit tests: detect chemical span in toy text
- [ ] AIM2-014-02  Load BioBERT weights
- [ ] AIM2-014-03  Span extraction function
- [ ] AIM2-014-04  CLI batch over parquet
- [ ] AIM2-014-05  Run \& pass unit tests


### AIM2-015  NER wrapper – LLM prompt-based

- [ ] AIM2-015-01  Write failing unit tests: mock LLM returns expected JSON
- [ ] AIM2-015-02  Prompt template design
- [ ] AIM2-015-03  Implement LangChain interface
- [ ] AIM2-015-04  Batch runner CLI
- [ ] AIM2-015-05  Run \& pass unit tests


### AIM2-016  NER post-processing \& ontology mapping

- [ ] AIM2-016-01  Write failing unit tests: fuzzy match “chlorogenic acid”→CURIE
- [ ] AIM2-016-02  Overlap resolution algorithm
- [ ] AIM2-016-03  Fuzzy synonym search via merged ontology
- [ ] AIM2-016-04  Output mapped JSONL
- [ ] AIM2-016-05  Run \& pass unit tests


### AIM2-017  Relation extraction runner

- [ ] AIM2-017-01  Write failing unit tests: extract predicate “affects” in sample
- [ ] AIM2-017-02  Design sliding-window splitter
- [ ] AIM2-017-03  Prompt templates for relations
- [ ] AIM2-017-04  Score \& store relations JSONL
- [ ] AIM2-017-05  Run \& pass unit tests


### AIM2-018  Predicate normalization \& validation

- [ ] AIM2-018-01  Write failing unit tests: “induces”→`affects` mapping
- [ ] AIM2-018-02  Mapping dictionary \& fall-back rule
- [ ] AIM2-018-03  Validator against ontology properties
- [ ] AIM2-018-04  Run \& pass unit tests


### AIM2-019  Species validation with NCBI taxonomy

- [ ] AIM2-019-01  Write failing unit tests: non-plant species removed
- [ ] AIM2-019-02  Load Taxonomy TSV index
- [ ] AIM2-019-03  Filter triples by TaxID lineage
- [ ] AIM2-019-04  Run \& pass unit tests


### AIM2-020  Triple exporter (RDF \& CSV)

- [ ] AIM2-020-01  Write failing unit tests: CSV columns subject,predicate,object
- [ ] AIM2-020-02  Implement RDF serialization
- [ ] AIM2-020-03  Implement CSV serialization
- [ ] AIM2-020-04  Run \& pass unit tests


### AIM2-021  Gold-standard annotation import

- [ ] AIM2-021-01  Write failing unit tests: 25 docs load into eval format
- [ ] AIM2-021-02  Brat parser
- [ ] AIM2-021-03  Doccano JSON parser
- [ ] AIM2-021-04  Unify into common schema
- [ ] AIM2-021-05  Run \& pass unit tests


### AIM2-022  Evaluation metrics module

- [ ] AIM2-022-01  Write failing unit tests: precision calc on dummy data
- [ ] AIM2-022-02  Entity scoring via seqeval
- [ ] AIM2-022-03  Relation scoring via scikit-learn
- [ ] AIM2-022-04  Run \& pass unit tests


### AIM2-023  Benchmark CLI

- [ ] AIM2-023-01  Write failing unit tests: CLI returns JSON report keys
- [ ] AIM2-023-02  Wire pipeline (extract → score)
- [ ] AIM2-023-03  Generate plots (precision-recall)
- [ ] AIM2-023-04  Run \& pass unit tests


### AIM2-024  Unit-test suite for ontology integrity

- [ ] AIM2-024-01  Write failing unit tests: orphan term detection
- [ ] AIM2-024-02  Implement integrity assertions
- [ ] AIM2-024-03  Run \& pass unit tests


### AIM2-025  Unit-test suite for extraction pipeline

- [ ] AIM2-025-01  Write failing unit tests: end-to-end triple count on toy corpus
- [ ] AIM2-025-02  Mock corpus \& expected outputs
- [ ] AIM2-025-03  Implement test runners
- [ ] AIM2-025-04  Run \& pass unit tests


### AIM2-026  Documentation site (mkdocs)

- [ ] AIM2-026-01  Write failing unit tests: mkdocs build exits 0
- [ ] AIM2-026-02  Create `mkdocs.yml`, install theme
- [ ] AIM2-026-03  Auto-generate API docs via `mkdocstrings`
- [ ] AIM2-026-04  GitHub Actions deploy to Pages
- [ ] AIM2-026-05  Run \& pass unit tests


### AIM2-027  PyPI release 0.1.0

- [ ] AIM2-027-01  Write failing unit tests: `pip install .` then `import aim2`
- [ ] AIM2-027-02  Bump version, update CHANGELOG
- [ ] AIM2-027-03  Build sdist \& wheels
- [ ] AIM2-027-04  Upload to TestPyPI, then PyPI
- [ ] AIM2-027-05  Run \& pass unit tests

