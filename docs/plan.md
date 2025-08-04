<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Development plan for stand-alone (and importable) Python programs

### Focus areas: Ontology development/management and literature information extraction.

*(Pipelines for LC-MS/MS, visualization, Docker, web UIs, etc. are **out of scope** as requested.)*

## 1. Code-base organization

```
aim2/
├── ontology/
│   ├── __init__.py
│   ├── ingest/
│   │   ├── chemont.py
│   │   ├── np_classifier.py
│   │   └── pmn.py
│   ├── transform/
│   │   ├── prune_terms.py
│   │   ├── merge_ontologies.py
│   │   └── add_relations.py
│   ├── export/
│   │   ├── to_owl.py
│   │   └── to_csv.py
│   └── utils.py
└── extraction/
    ├── __init__.py
    ├── corpus_builder.py
    ├── ner/
    │   ├── models.py
    │   └── postprocess.py
    ├── relation/
    │   ├── prompts.py
    │   └── extract.py
    ├── mapping.py
    └── evaluation.py
```

*Each sub-module can run as `python -m aim2.ontology.prune_terms …` **or** be imported elsewhere.*

## 2. Ontology development \& management

### 2.1 Technology stack

- **owlready2** for programmatic OWL loading/editing/saving[^1][^2].
- **OntoBio** or **OAK** for higher-level helpers (term search, synonyms, CURIE handling)[^3][^4].
- **pronto** for fast parsing of OBO/OWL when only reading is needed[^5].
- **pandas** for tabular transforms, **rdflib** for SPARQL when necessary.


### 2.2 Key scripts

| Script | Purpose | Usage example |
| :-- | :-- | :-- |
| `ingest/*.py` | Download/latest-dump loader for ChemOnt, NP-Classifier JSON, PMN CSV, Plant Ontology OBO, etc. Stores each as Owlready2 ontology objects. | `python -m aim2.ontology.ingest.chemont --out data/raw/chemont.owl` |
| `transform/prune_terms.py` | Input OWL → keep only terms mapping to metabolites; drop children with no LC-MS evidence. Rule-based filters + CLI flags (`--target-count 293`). |  |
| `transform/merge_ontologies.py` | Merge structural, source, and functional ontologies into one namespace; resolve duplicate IDs via exact-string or xref match (uses Owlready2 `ontology.merge_onto()` pattern[^6]). |  |
| `transform/add_relations.py` | Programmatically add AIM2-specific object properties (`affects`, `accumulates_in`, etc.) and assert hierarchy (`upregulates` subPropertyOf `involved_in`). |  |
| `export/to_owl.py` | Save merged ontology to version-tagged OWL; optionally emit CSV summaries for GitHub browsing. |  |

### 2.3 Design choices

1. **Namespaces**: prefix each source (`CHEMONT:`, `PO:`) and maintain mapping table to avoid IRI clashes.
2. **Extensibility**: allow `--custom-tsv` to append lab-specific terms without editing code.
3. **Testing**: small unit tests ensuring term counts and parent relations remain stable.

## 3. Literature information-extraction

### 3.1 Corpus builder (`corpus_builder.py`)

Steps

1. Keyword queries → PubMed API → PMID list.
2. For each PMID:
    - Try **E-utilities efetch** (XML).
    - If not open-access, save abstract only.
    - PDFs: fallback to **httplib2** + *GROBID* (local java service) for conversion; obey `robots.txt`.
3. Store cleaned text in parquet with metadata (PMID, journal, year, taxon hits) for incremental runs.

### 3.2 Named-entity recognition

- Wrap multiple back-ends in `ner.models`:

| Model wrapper | Library | Notes |
| :-- | :-- | :-- |
| `BioBERT_CHEM` | Transformers | Finetuned for chemicals. |
| `CyBERT_PO` | HuggingFace | Recognizes plant anatomy. |
| `AIONER` multi-type | Pretrained weights[^7] | Good baseline. |
| `LLM_Call` | OpenAI/Gemini via `langchain` | Prompt-based zero/few-shot. LLM-IE API interface[^8][^9]. |

`postprocess.py` handles:

- span-to-ontology mapping via fuzzy match against merged ontology labels/synonyms.
- deduplication across overlapping spans.


### 3.3 Relation extraction (`relation/extract.py`)

- Prompt templates in `prompts.py` encode task schema (Subject, Predicate, Object, Evidence sentence).
- Sliding-window strategy—texts chunked at 2,048 tokens; entity dictionary passed in-prompt to improve grounding.
- Output JSONL rows with scored relations.


### 3.4 Mapping to ontology and cleaning (`mapping.py`)

1. Convert surface forms to CURIEs.
2. Validate species using NCBI Taxonomy lookup; drop non-plant hits.
3. Normalize predicates to ontology object-properties; fallback to generic `related_to`.

### 3.5 Evaluation (`evaluation.py`)

- Load gold-standard Brat/Doccano annotations of ~25 papers.
- Compute precision/recall/F1 per entity type and per relation using **seqeval** and **scikit-learn**.
- CLI supports `--model llama-70b-chat` to benchmark new LLMs.


## 4. Inter-module link

After extraction, triples (`compound CURIE`, `predicate`, `target CURIE`) are exported as RDF or CSV and can be re-imported by ontology scripts to enrich axioms or annotations.

## 5. Packaging \& distribution

1. `pyproject.toml` with optional extras:
    - `pip install aim2[owl]` → owlready2, rdflib.
    - `pip install aim2[nlp]` → transformers, sentencepiece, langchain.
2. Publish to PyPI; semantic versioning (`0.1.0` initial).
3. GitHub Actions:
    - Lint (ruff), unit tests, ontology integrity check.
    - Upload built OWL/CSV artifacts to `gh-pages` for easy download.

## 6. Example workflows

### 6.1 Build ontology from scratch

```bash
# 1. Ingest
python -m aim2.ontology.ingest.chemont
python -m aim2.ontology.ingest.np_classifier
# 2. Merge + prune
python -m aim2.ontology.merge_ontologies --inputs chemont.owl np_classifier.owl \
    --output merged.owl
python -m aim2.ontology.prune_terms merged.owl --target-count 293
# 3. Add AIM2 relations
python -m aim2.ontology.add_relations pruned.owl --output aim2_core.owl
```


### 6.2 Extract relations from a new PubMed query

```bash
python -m aim2.extraction.corpus_builder --query "Arabidopsis drought metabolite"
python -m aim2.extraction.ner.models --engine biobert --input corpus.parquet
python -m aim2.extraction.relation.extract --model gpt-4o --input ner_output.jsonl
python -m aim2.extraction.mapping --relations relations.jsonl --ontology aim2_core.owl
```


## 7. Milestones \& timeline (3-month sprint view)

| Week | Deliverable |
| :-- | :-- |
| 1-2 | Ingest loaders, initial OWL parsing; publish v0.0.1. |
| 3-4 | Pruning \& merge logic; CLI + tests. |
| 5-6 | Corpus builder MVP; baseline BioBERT NER. |
| 7-8 | Ontology-based span mapping; relation prompts. |
| 9-10 | Gold-standard annotation and evaluation module. |
| 11 | Performance benchmark report. |
| 12 | Documentation \& PyPI release v0.1.0. |

## 8. Future extensions

- Add **mOWL** graph-embedding support for similarity queries[^10].
- Integrate **DeepOnto** for ontology alignment with deep-learning[^11].
- Provide optional **LangExtract** backend for Gemini models[^12].

These modular Python programs give AIM2 a reproducible foundation for ontology curation and literature-driven information extraction while remaining stand-alone, scriptable, and easily imported into larger pipelines.

<div style="text-align: center">⁂</div>

[^1]: https://pypi.org/project/owlready2/

[^2]: https://owlready2.readthedocs.io

[^3]: https://github.com/biolink/ontobio

[^4]: http://berkeleybop.github.io/software/OAK/

[^5]: https://pypi.org/project/pronto/

[^6]: https://stackoverflow.com/questions/75102102/merge-two-ontologies-using-owlready

[^7]: https://academic.oup.com/bioinformatics/article/39/5/btad310/7160912

[^8]: https://pubmed.ncbi.nlm.nih.gov/40078164/

[^9]: https://arxiv.org/abs/2411.11779

[^10]: https://pmc.ncbi.nlm.nih.gov/articles/PMC9848046/

[^11]: https://arxiv.org/html/2307.03067v2

[^12]: https://developers.googleblog.com/en/introducing-langextract-a-gemini-powered-information-extraction-library/

[^13]: https://github.com/emmo-repo/EMMOntoPy

[^14]: https://pmc.ncbi.nlm.nih.gov/articles/PMC9931203/

[^15]: https://github.com/shuzhao-li-lab/PythonCentricPipelineForMetabolomics

[^16]: https://owlready2.readthedocs.io/en/latest/mixing_python_owl.html

[^17]: https://journals.plos.org/ploscompbiol/article?id=10.1371%2Fjournal.pcbi.1011912

[^18]: https://github.com/librairy/bio-ner

[^19]: https://pmc.ncbi.nlm.nih.gov/articles/PMC7602939/

[^20]: https://pythonhosted.org/Owlready/mixing_python_owl.html

