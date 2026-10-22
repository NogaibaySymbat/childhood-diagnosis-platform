# Technology Comparison

**Document:** 05-technology-comparison.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Author:** Sagynbaeva Zhanat
**Last updated:** 2026-10-__
**Status:** Draft for pre-defense 1

---

## 1. Purpose

This document records the key technical decisions of the project, the alternatives considered for each, the criteria used to choose between them, and the rationale for the final choice. Each decision is traced to the requirements in `docs/06-requirements.md` that drove it.

The committee assesses whether technology choices are justified by the project requirements — not whether the chosen technology is popular.

## 2. Decision summary

| # | Decision | Chosen | Main reason |
|---|---|---|---|
| D-1 | Backend platform | | |
| D-2 | Data storage | | |
| D-3 | Classification approach | | |
| D-4 | Component interaction | | |
| D-5 | Frontend | | |

---

## 3. D-1 — Backend platform

**Requirements that drive this decision:** NFR-01 (response under 3 s), NFR-06 and NFR-07 (security), NFR-11 (knowledge base separate from code), FR-27 (knowledge base editable without redeployment).

| Criterion | Weight | FastAPI (Python) | Django (Python) | Node.js + Express |
|---|---|---|---|---|
| Integration with the ML stack | | | | |
| Response time under load | | | | |
| Built-in admin for the knowledge base | | | | |
| Team experience | | | | |
| Ecosystem for medical/data work | | | | |
| Development speed for this scope | | | | |

**Chosen:**

**Rationale:**

**Trade-off accepted:**

---

## 4. D-2 — Data storage

**Requirements:** NFR-07 (encryption at rest), NFR-08 (full deletion of a child's data), FR-16 and FR-17 (check history), NFR-11 (knowledge base separate from code).

| Criterion | Weight | PostgreSQL | MongoDB | SQLite |
|---|---|---|---|---|
| Structured relations (child → checks → messages) | | | | |
| Encryption at rest | | | | |
| Cascading deletion of personal data | | | | |
| Storing a flexible knowledge base | | | | |
| Suitability for production deployment | | | | |
| Operational complexity for a 3-person team | | | | |

**Chosen:**

**Rationale:**

**Trade-off accepted:**

---

## 5. D-3 — Classification approach

This is the project's central technical decision. Note that the triage layer is **not** part of it: urgency is determined by deterministic rules derived from clinical protocols, separately from the ranking (see `docs/07-architecture.md`). This comparison is only about how the ranked list of likely conditions is produced.

**Requirements:** NFR-02 (top-3 accuracy ≥ 75 %), NFR-03 (urgent-case recall ≥ 90 %), FR-11 (explain which symptoms support each condition), NFR-10 (degrade gracefully if the model is unavailable).

| Criterion | Weight | Rule-based / knowledge base | Classical ML (Random Forest, Gradient Boosting) | Neural network | LLM with retrieval |
|---|---|---|---|---|---|
| Explainability of the result | | | | | |
| Traceability to clinical protocols | | | | | |
| Training data required | | | | | |
| Reproducibility of output | | | | | |
| Expected quality on the target metrics | | | | | |
| Effort within the project timeline | | | | | |

**Chosen:**

**Rationale:**

**Trade-off accepted:**

**Baseline for comparison:** the rule-based classifier over the knowledge base. The chosen model must beat it on the metrics in `docs/08-verification-plan.md`, otherwise the simpler solution wins.

---

## 6. D-4 — Component interaction

**Requirements:** NFR-01 (response time), NFR-10 (triage still works if the model is down), NFR-12 (traceability).

| Criterion | Weight | Monolith | REST API between services | Message queue |
|---|---|---|---|---|
| Latency | | | | |
| Isolation of a model failure | | | | |
| Deployment complexity | | | | |
| Debugging and testing | | | | |
| Fit to the team size | | | | |

**Chosen:**

**Rationale:**

**Trade-off accepted:**

---

## 7. D-5 — Frontend

**Requirements:** NFR-05 (360–1920 px, modern browsers), FR-26 (Russian, Kazakh, English), FR-05 to FR-14 (symptom intake and results).

| Criterion | Weight | React | Vue | Server-rendered templates |
|---|---|---|---|---|
| Interactive symptom intake | | | | |
| Internationalisation support | | | | |
| Responsive behaviour | | | | |
| Team experience | | | | |
| Build and deployment complexity | | | | |

**Chosen:**

**Rationale:**

**Trade-off accepted:**

---

## 8. Dataset sources

No open symptom-to-diagnosis dataset exists for pediatric care in Kazakhstan. The knowledge base is therefore built from clinical protocols, and open datasets are used only for training and validating the model architecture.

| Source | Type | Use in this project | Limitation |
|---|---|---|---|
| Clinical protocols of the Ministry of Health RK | Protocols | Knowledge base, triage rules | Not machine-readable; manual extraction |
| WHO IMCI | Protocols | Triage rules and danger signs | Covers ages 0–5 only |
| DDXPlus | Synthetic dataset | Model training and architecture validation | Synthetic; not pediatric-specific |
| SymCat | Symptom–condition database | Knowledge base cross-check | English only; general population |

This limitation is recorded as a risk in `docs/01-proposal.md` and will be stated explicitly at the defense.

## 9. Open decisions

Decisions not yet made, with the date by which they must be:

| Decision | Blocked by | Needed by |
|---|---|---|
| | | |

## 10. References

Links to documentation, benchmarks and publications used to fill the comparison tables above. Every performance claim in those tables cites a source here.

-
