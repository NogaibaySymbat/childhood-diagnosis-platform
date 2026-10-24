# Supervisor Feedback

**Document:** 10-supervisor-feedback.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Supervisor:** Yermaganbetova Madina Askarovna
**Last updated:** 2026-10-24
**Status:** Draft for the first probationary submission

---

## 1. How supervisor review is handled

Deliverables are reviewed by the supervisor before each submission. Each comment is recorded here together with the decision taken on it and a reference to the document or change that implemented it, so that the effect of the review is traceable rather than asserted.

## 2. Review log

| # | Date | Deliverable reviewed | Comment | Decision | Implemented in |
|---|---|---|---|---|---|
| 1 | *22.03.2026* | Project proposal | The list of diseases is too broad for the scope of a diploma project | Narrowed the scope to the most frequent childhood conditions — respiratory, gastrointestinal, ENT and common skin conditions — in children aged 0–14, with everything else stated as out of scope | `docs/01-proposal.md`, section 8 (Project scope); reflected in `docs/03-literature-review.md` and in the knowledge-base scope in `docs/07-architecture.md` |
| 2 | *22.03.2025* | Project proposal | Expected outcomes are stated without measurable indicators | Added target quality indicators and made them verifiable: top-3 accuracy ≥ 75 %, recall on urgent cases ≥ 90 %, median response time < 3 s, with urgent-case recall taking precedence over ranking accuracy | `docs/01-proposal.md`, section 7; NFR-01 to NFR-03 in `docs/06-requirements.md`; metrics M-1 to M-6 and acceptance thresholds in `docs/08-verification-plan.md` |
| 3 | *22.03.2026* | Project concept and data sources | Raised the question of which datasets the model would be trained on, and how the model is integrated rather than treated as the whole system; asked for the work to be framed as a platform rather than a single classifier | Documented the dataset situation openly: no real paediatric symptom-to-diagnosis corpus exists for the local context, so the knowledge base is transcribed from the clinical protocols of the MoH RK and open datasets (DDXPlus, SymCat) are used only for training and validating the architecture. The model was reduced to one component — ranking — with a separate deterministic triage engine, and the scope was widened to a platform with a pediatrician dashboard and request routing | Dataset sources in `docs/05-technology-comparison.md`, section 8; the hybrid decision in section 5 of the same document; component separation in `docs/07-architecture.md`, sections 4 and 5; FR-19 to FR-23 (pediatrician workflow) in `docs/06-requirements.md` |

> **Note on dates.** The dates of these conversations are to be filled in by the team before submission; the comments themselves are recorded as given.

## 3. Effect of the review on the project

The three comments changed the project substantially rather than cosmetically.

**Scope.** The first comment removed the implicit assumption that the platform would cover childhood disease in general. The scope section now names the condition groups covered and, equally important, what is excluded. This also made the knowledge-base transcription work finite and therefore schedulable.

**Measurability.** The second comment turned expected outcomes from descriptions into commitments. It is the reason the project has a verification plan with a baseline at all: once a target number exists, it has to be measured against something.

**Architecture.** The third comment had the largest effect. Treating the model as one component inside a platform, rather than as the system itself, led directly to the separation of the ranking model from the deterministic triage engine — which is now the project's central design property and the reason an urgent case cannot be missed because of a model error. The same conversation forced the dataset limitation to be stated openly rather than discovered later.

## 4. Decisions still requiring supervisor confirmation

| # | Question | Why it needs the supervisor | Document affected |
|---|---|---|---|
| 1 | Final list of conditions for the knowledge base within the narrowed scope | Determines the size of the protocol transcription work and the coverage claims | `docs/03-literature-review.md`, `docs/07-architecture.md` |
| 2 | Clinical correctness of the red-flag set transcribed from protocols | Safety-critical; must be confirmed by a clinician | `docs/07-architecture.md` |
| 3 | Composition of the protocol-derived test set and its reference labels | The test set is authored by the team from the same protocols used to build the system; supervisor review is the only external check | `docs/08-verification-plan.md` |
| 4 | Whether the stated target metrics are realistic given the available data | They are commitments made in front of the committee | `docs/01-proposal.md`, `docs/08-verification-plan.md` |
| 5 | Deployment environment available for the defense | Determines whether performance can be measured under realistic conditions | `docs/05-technology-comparison.md`, `docs/08-verification-plan.md` |
| 6 | Whether asynchronous interviews are acceptable as evidence of relevance | Affects how the evidence section is presented | `docs/02-relevance-evidence.md` |

## 5. Next review

Scheduled before the second pre-defense, with the knowledge base and the trained model as the deliverables under review. Comments from that meeting will be appended to the log in section 2 together with the commits that implement them.
