# Technology Comparison

**Document:** 05-technology-comparison.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Author:** Sagynbaeva Zhanat
**Last updated:** 2026-10-22
**Status:** Draft for pre-defense 1

---

## 1. Purpose

This document records the key technical decisions of the project, the alternatives considered for each, the criteria used to choose between them, and the rationale for the final choice. Each decision is traced to the requirements in `docs/06-requirements.md` that drove it.

Scores are on a 1–5 scale, where 5 is best for the stated criterion in the context of this project. Weights are High, Medium or Low, reflecting how much the criterion matters given the requirements.

## 2. Decision summary

| # | Decision | Chosen | Main reason |
|---|---|---|---|
| D-1 | Backend platform | FastAPI (Python) | Runs in the same process as the model; async request handling keeps NFR-01 reachable |
| D-2 | Data storage | PostgreSQL | Relational integrity for child → checks → messages, encryption at rest, cascading deletion for NFR-08 |
| D-3 | Classification approach | Knowledge base + gradient boosting, hybrid | Explainable, traceable to protocols, and meets NFR-02 without data the project does not have |
| D-4 | Component interaction | Modular monolith with an internal model service boundary | Lowest latency and complexity for three developers, while keeping NFR-10 satisfiable |
| D-5 | Frontend | React | Interactive symptom intake and mature internationalisation for three languages |

---

## 3. D-1 — Backend platform

**Requirements that drive this decision:** NFR-01 (response under 3 s), NFR-06 and NFR-07 (security), NFR-11 (knowledge base separate from code), FR-27 (knowledge base editable without redeployment).

| Criterion | Weight | FastAPI (Python) | Django (Python) | Node.js + Express |
|---|---|---|---|---|
| Integration with the ML stack | High | 5 | 5 | 2 |
| Response time under load | High | 5 | 3 | 5 |
| Built-in admin for the knowledge base | Medium | 2 | 5 | 2 |
| Team experience | High | 4 | 3 | 3 |
| Ecosystem for medical/data work | Medium | 5 | 4 | 2 |
| Development speed for this scope | Medium | 5 | 4 | 4 |

**Chosen:** FastAPI.

**Rationale:** The model is a Python artefact, so a Python backend lets the assessment run in-process without a serialisation boundary — the single largest contributor to response time under NFR-01. FastAPI's async request handling suits a workload where most of the time is spent waiting on the database and the model. Pydantic validation covers the input constraints in FR-07 and FR-08 (duration required, temperature within 34–42 °C) declaratively, so validation lives in one place instead of being scattered through handlers.

**Trade-off accepted:** Django would have given a ready-made admin interface for editing the knowledge base (FR-27), which FastAPI does not. We accept building a small editing interface ourselves, because the knowledge base is a structured dataset with a narrow edit surface, not a general CRUD application, and Django's ORM-centred design would otherwise constrain how the knowledge base is stored.

---

## 4. D-2 — Data storage

**Requirements:** NFR-07 (encryption at rest), NFR-08 (full deletion of a child's data), FR-16 and FR-17 (check history), NFR-11 (knowledge base separate from code).

| Criterion | Weight | PostgreSQL | MongoDB | SQLite |
|---|---|---|---|---|
| Structured relations (child → checks → messages) | High | 5 | 3 | 4 |
| Encryption at rest | High | 5 | 4 | 2 |
| Cascading deletion of personal data | High | 5 | 3 | 4 |
| Storing a flexible knowledge base | Medium | 4 | 5 | 3 |
| Suitability for production deployment | High | 5 | 4 | 2 |
| Operational complexity for a 3-person team | Medium | 4 | 3 | 5 |

**Chosen:** PostgreSQL.

**Rationale:** The data model is inherently relational: a parent account owns child profiles, a profile owns checks, a check owns an assessment and may own a message thread with a pediatrician. NFR-08 requires that deleting a child removes the profile, its checks and its messages — foreign keys with `ON DELETE CASCADE` make this a property of the schema rather than application code that can be forgotten. Encryption at rest is available at the instance level, and the `JSONB` type stores the knowledge base as structured documents inside the same database, so NFR-11 is satisfied without a second storage system.

**Trade-off accepted:** MongoDB would model the knowledge base more naturally, since symptom–condition entries have varying shapes. We accept slightly more rigid knowledge-base storage in exchange for referential integrity on personal data, because a missed deletion is a privacy failure while an awkward schema is only inconvenient. SQLite was rejected for production despite its simplicity: concurrent writes from the parent application and the doctor dashboard would serialise.

---

## 5. D-3 — Classification approach

This is the project's central technical decision. The triage layer is **not** part of it: urgency levels and red flags are produced by deterministic rules derived from clinical protocols, separately from the ranking (see `docs/07-architecture.md`). This comparison concerns only how the ranked list of likely conditions is produced.

**Requirements:** NFR-02 (top-3 accuracy ≥ 75 %), NFR-03 (urgent-case recall ≥ 90 %), FR-11 (explain which symptoms support each condition), NFR-10 (degrade gracefully if the model is unavailable).

| Criterion | Weight | Rule-based / knowledge base | Classical ML (Gradient Boosting) | Neural network | LLM with retrieval |
|---|---|---|---|---|---|
| Explainability of the result | High | 5 | 4 | 2 | 2 |
| Traceability to clinical protocols | High | 5 | 3 | 2 | 2 |
| Training data required | High | 5 | 3 | 1 | 4 |
| Reproducibility of output | High | 5 | 5 | 5 | 1 |
| Expected quality on the target metrics | High | 3 | 4 | 4 | 3 |
| Effort within the project timeline | Medium | 4 | 4 | 2 | 3 |

**Chosen:** a hybrid — the knowledge base produces the candidate set and the per-symptom explanation, and a gradient-boosting classifier trained on DDXPlus (filtered to the paediatric age range) reorders the candidates by likelihood.

**Rationale:** The two requirements that decide this are FR-11 and NFR-03. FR-11 demands that the interface show which reported symptoms support each listed condition and which expected symptoms are absent; that mapping exists in the knowledge base and is not something a model is asked to invent. NFR-03 makes a missed urgent case the costliest error, which is why urgency never depends on the ranking at all. Within those constraints the model's job is narrow — ordering a candidate set — and gradient boosting handles tabular, sparse, binary symptom features well at this scale, while remaining inspectable through feature importances.

A neural network was rejected on data grounds: no paediatric symptom–diagnosis dataset exists for the local context (section 8), and a network trained on a synthetic dataset would generalise worse than a simpler model while being harder to explain. An LLM was rejected as the source of the result: its output is not reproducible, cannot be traced to a protocol, and carries no measurable quality guarantee — none of which is acceptable when the output influences whether a parent takes a child to a doctor. An LLM may later be used at the *input* boundary, to turn a parent's free-text description into a structured symptom set, where an error is visible to the user and correctable before the assessment runs.

**Trade-off accepted:** The hybrid is harder to evaluate than a single model, because quality depends on both the knowledge base's coverage and the reordering. We accept this in exchange for an explanation that is correct by construction, and for a system that still answers when the model is unavailable (NFR-10).

**Baseline for comparison:** the knowledge base alone, ranking candidates by the share of matching symptoms. The hybrid must beat this baseline on the metrics defined in `docs/08-verification-plan.md`; if it does not, the simpler solution ships.

---

## 6. D-4 — Component interaction

**Requirements:** NFR-01 (response time), NFR-10 (triage still works if the model is down), NFR-12 (traceability).

| Criterion | Weight | Modular monolith | REST API between separate services | Message queue |
|---|---|---|---|---|
| Latency | High | 5 | 4 | 2 |
| Isolation of a model failure | High | 4 | 5 | 5 |
| Deployment complexity | High | 5 | 3 | 2 |
| Debugging and testing | Medium | 5 | 3 | 2 |
| Fit to the team size | High | 5 | 3 | 2 |

**Chosen:** a modular monolith — one deployable application with an explicit internal boundary around the model component.

**Rationale:** Three developers with two days a week on this project cannot operate several services well, and the request path is a single synchronous interaction with no long-running work to offload. Keeping the model in-process removes a network hop from the critical path for NFR-01. The boundary around the model component is nevertheless explicit — a single interface the rest of the application calls — so NFR-10 is satisfiable by catching failures at that boundary and returning the triage result and red flags without the ranked list.

**Trade-off accepted:** A model failure can affect the whole process in a way separate services would prevent. We accept this because the degradation path required by NFR-10 is implemented at the interface, and because the boundary leaves the option of extracting the model into its own service later without rewriting callers.

---

## 7. D-5 — Frontend

**Requirements:** NFR-05 (360–1920 px, current Chrome, Safari and Firefox), FR-26 (Russian, Kazakh, English), FR-05 to FR-14 (symptom intake and results).

| Criterion | Weight | React | Vue | Server-rendered templates |
|---|---|---|---|---|
| Interactive symptom intake | High | 5 | 5 | 2 |
| Internationalisation support | High | 5 | 4 | 3 |
| Responsive behaviour | Medium | 4 | 4 | 4 |
| Team experience | High | 4 | 2 | 3 |
| Build and deployment complexity | Medium | 3 | 4 | 5 |

**Chosen:** React.

**Rationale:** Symptom intake is the most interactive part of the product — search with live filtering, chips that are added and removed, a selection that persists while the user scrolls a long list (FR-05, FR-06, FR-09). Doing this with page reloads would make the primary flow slow and awkward. React has the team's experience behind it and the most mature internationalisation tooling for the three-language requirement in FR-26, including plural rules, which differ between Russian and Kazakh.

**Trade-off accepted:** A build step and a heavier initial page load compared with server-rendered templates. We accept this because the intake flow is used repeatedly within a session, where interaction cost matters more than first load.

---

## 8. Dataset sources

No open symptom-to-diagnosis dataset exists for paediatric care in Kazakhstan. The knowledge base is therefore built from clinical protocols, and open datasets are used only for training and validating the model architecture.

| Source | Type | Use in this project | Limitation |
|---|---|---|---|
| Clinical protocols of the Ministry of Health RK | Protocols | Knowledge base, triage rules | Not machine-readable; manual extraction |
| WHO IMCI | Protocols | Triage rules and danger signs | Covers ages 0–5 only |
| DDXPlus | Synthetic dataset | Model training and architecture validation | Synthetic; not paediatric-specific |
| SymCat | Symptom–condition database | Knowledge base cross-check | English only; general population |

This limitation is recorded as a risk in `docs/01-proposal.md` and will be stated explicitly at the defense.

## 9. Open decisions

| Decision | Blocked by | Needed by |
|---|---|---|
| Deployment environment (university server, cloud, local) | Supervisor confirmation of what is available | Before implementation starts |
| Whether an LLM is used at the input boundary for free-text symptom description | Results of the first evaluation round | Pre-defense 2 |
| Exact set of conditions covered by the knowledge base | `docs/03-literature-review.md` | Before knowledge base construction |

## 10. References

Links to documentation and publications used to fill the comparison tables above are collected here. Each performance claim cites a source.

- FastAPI documentation — https://fastapi.tiangolo.com
- PostgreSQL documentation, foreign keys and `JSONB` — https://www.postgresql.org/docs/
- DDXPlus: A New Dataset For Automatic Medical Diagnosis — https://arxiv.org/abs/2205.09148
- WHO Integrated Management of Childhood Illness — https://www.who.int/teams/maternal-newborn-child-adolescent-health-and-ageing/child-health/integrated-management-of-childhood-illness
