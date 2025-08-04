<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# AIM2 Software Development Ticket Backlog

Ticket IDs use the prefix `AIM2-` for easy cross-reference.
“Depends on” lists **direct** blockers; tickets not listed can proceed in parallel.


| ID | Title | Description (Done = ✅) | Independent? | Depends on |
| :-- | :-- | :-- | :-- | :-- |
| AIM2-001 | Project skeleton \& packaging | -  Generate `aim2/` package layout shown in plan.  <br>-  Add `pyproject.toml`, `README`, `ruff` \& `pytest` config.  <br>-  CI workflow that runs lint + tests. | No | — |
| AIM2-002 | Ingest loader – ChemOnt | CLI script to download/parse latest ChemOnt OWL → Owlready2 object, save to `data/raw/chemont.owl`. | Yes | AIM2-001 |
| AIM2-003 | Ingest loader – NP-Classifier | Same pattern for NP-Classifier JSON. | Yes | AIM2-001 |
| AIM2-004 | Ingest loader – Plant Metabolic Network | Download PMN compounds table, convert to OWL individuals. | Yes | AIM2-001 |
| AIM2-005 | Ingest loader – Plant Ontology (PO) | Fetch PO OBO, convert to OWL. | Yes | AIM2-001 |
| AIM2-006 | Ingest loader – NCBI Taxonomy snapshot | Script to pull NCBI taxonomy dump, keep plants only, export TSV. | Yes | AIM2-001 |
| AIM2-007 | Ingest loader – PECO \& GO | Separate CLI for PECO; second for GO slim subset. | Yes | AIM2-001 |
| AIM2-008 | Ontology merge engine | Merge multiple source ontologies, resolve duplicate IRIs, maintain source namespaces; output `merged.owl`. | No | AIM2-002,-003,-004,-005,-007 |
| AIM2-009 | Ontology pruning utility | Implement rules/CLI (`--target-count`) to reduce categories (e.g., 2,008 → 293). | No | AIM2-008 |
| AIM2-010 | Add AIM2-specific object properties | Script to create properties (`affects`, `accumulates_in`, etc.) and declare hierarchies. | No | AIM2-009 |
| AIM2-011 | Ontology exporters | -  `to_owl.py` writes versioned OWL. <br>-  `to_csv.py` dumps label table for GitHub browsing. | No | AIM2-010 |
| AIM2-012 | Corpus builder MVP | Given PubMed query, download abstracts \& OA XML, attempt PDF → text (GROBID), store parquet with metadata. | Yes | AIM2-001 |
| AIM2-013 | Corpus incremental update \& dedup | Detect previously parsed PMIDs; only fetch new. | No | AIM2-012 |
| AIM2-014 | NER wrapper – Transformers models | Load BioBERT/CyBERT checkpoints, return entity spans + types. | Yes | AIM2-001 |
| AIM2-015 | NER wrapper – LLM prompt-based | Generic interface to OpenAI/Gemini via LangChain; configurable prompt template. | Yes | AIM2-001 |
| AIM2-016 | NER post-processing \& ontology mapping | Fuzzy match spans to merged ontology labels/synonyms; output CURIEs, resolve overlaps. | No | AIM2-014 or -015, plus AIM2-010 |
| AIM2-017 | Relation-extraction prompts \& runner | Implement sliding-window prompting, return JSONL (subject, predicate, object, evidence, score). | No | AIM2-012, AIM2-016 |
| AIM2-018 | Predicate normalization \& validation | Map free-text predicates to ontology object properties; default to `related_to` if unmatched. | No | AIM2-017, AIM2-010 |
| AIM2-019 | Species validation with NCBI taxonomy | Filter triples: keep only plant species; drop irrelevant. | No | AIM2-016, AIM2-006 |
| AIM2-020 | Triple exporter (RDF \& CSV) | Serialize cleaned triples for downstream loading. | No | AIM2-018, AIM2-019 |
| AIM2-021 | Gold-standard annotation import | Convert Brat/Doccano files (≈25 papers) to evaluation format. | Yes | AIM2-001 |
| AIM2-022 | Evaluation metrics module | Compute per-type \& per-relation precision/recall/F1 using seqeval \& scikit-learn. | No | AIM2-021 |
| AIM2-023 | Benchmark CLI | Run extraction pipeline for a model, compare to gold standard, output report/plots. | No | AIM2-022, AIM2-020 |
| AIM2-024 | Unit-test suite for ontology integrity | Tests: no orphan terms, expected counts, property hierarchy intact. | Yes | AIM2-010 |
| AIM2-025 | Unit-test suite for extraction pipeline | Mock small corpus; ensure NER + mapping + relation flow produces expected triples. | Yes | AIM2-020 |
| AIM2-026 | Documentation site (mkdocs) | Install mkdocs-material; auto-publish API docs \& CLI examples via GitHub Actions. | No | AIM2-001, AIM2-011, AIM2-020 |
| AIM2-027 | PyPI release 0.1.0 | Tag, build, and publish; include extras `[owl]` and `[nlp]`. | No | AIM2-011, AIM2-020, AIM2-024, AIM2-025 |

### Dependency graph highlights

- **Build‐order bottleneck:** AIM2-008 → AIM2-009 → -010 drives the ontology path; AIM2-012 kick-starts the literature path.
- Testing (AIM2-024/-025) can start as soon as their respective code areas stabilize; they block the final PyPI release.
- Documentation and benchmarking rely on artifacts from both domains but do not block core functionality.

This ticket set lets multiple developers work concurrently while making dependencies explicit, ensuring the AIM2 ontology and extraction capabilities converge smoothly toward the 0.1.0 milestone.

<div style="text-align: center">⁂</div>

[^1]: https://github.com/INCATools/ontology-development-kit

[^2]: https://www.scitepress.org/Papers/2023/122377/122377.pdf

[^3]: http://berkeleybop.github.io/best_practice/

[^4]: https://www.mdpi.com/2504-2289/7/2/101

[^5]: https://blog.palantir.com/ontology-oriented-software-development-68d7353fdb12

[^6]: https://blog.invgate.com/software-dependencies

[^7]: https://teamhub.com/blog/understanding-dependency-management-in-software-development/

[^8]: https://www.youtube.com/watch?v=M-e-h2ujlCo

[^9]: https://packaging.python.org/tutorials/managing-dependencies/

[^10]: https://devops.com/proactive-dependency-management-reducing-risk-and-improving-software-quality/

[^11]: https://academic.oup.com/database/article/doi/10.1093/database/baae133/7972659

[^12]: https://docs.openedx.org/projects/openedx-proposals/en/latest/best-practices/oep-0018-bp-python-dependencies.html

[^13]: https://blog.codacy.com/software-dependency-management

[^14]: https://wellcomeopenresearch.org/articles/10-360/pdf

[^15]: https://www.reddit.com/r/Python/comments/1gphzn2/a_completeish_guide_to_dependency_management_in/

[^16]: https://jfrog.com/blog/out-with-the-old-keeping-your-software-secure-by-managing-dependencies/

[^17]: https://palantir.com/docs/foundry/getting-started/application-reference/

[^18]: https://aws.amazon.com/blogs/big-data/amazon-mwaa-best-practices-for-managing-python-dependencies/

[^19]: https://finitestate.io/blog/open-source-dependency-management-iot

[^20]: https://www.digitalocean.com/community/tutorials/how-to-package-and-distribute-python-applications

