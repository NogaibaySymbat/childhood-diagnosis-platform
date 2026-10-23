# Project Proposal

**Document:** 01-proposal.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Last updated:** 2026-10-20
**Status:** Draft for pre-defense 1

---

## 1. Project title

| Language | Title |
|---|---|
| ENG | Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases |
| RUS | Разработка интеллектуальной платформы предварительной диагностики детских заболеваний |
| KAZ | Балалар ауруларын алдын ала диагностикалаудың зияткерлік платформасын әзірлеу |

## 2. Team and supervisor

| Name | Group | Role in the project |
|---|---|---|
| Nogaibay Symbat | SE-2402 | Backend, repository and project structure, literature review |
| Sagynbaeva Zhanat | SE-2402 | Machine learning model, architecture, technology comparison |
| Sabyr Aruzhan | SE-2402 | Requirements, user research, frontend and UX |

**Supervisor:** Yermaganbetova Madina Askarovna

**Repository:** https://github.com/NogaibaySymbat/childhood-diagnosis-platform
**Task tracker:** https://powerpuff.youtrack.cloud/projects/DIP

## 3. Problem statement

Parents of young children routinely face symptoms they cannot interpret. A fever of 38.5 °C may be a self-limiting viral infection or the first sign of a condition that needs same-day medical attention, and the difference is not obvious without clinical training. This produces three concrete problems:

1. **Delayed treatment.** Parents wait, assuming the symptoms will pass, and seek help only when the child's condition has deteriorated.
2. **Self-medication.** Medicines are given without indication, including antibiotics for viral infections, which is a recognised driver of antimicrobial resistance.
3. **Load on primary care.** Pediatric clinics in Kazakhstan receive a large share of visits that do not require an in-person examination, while urgent cases wait in the same queue.

Existing online symptom checkers do not resolve this for the local context: they are built around adult symptom profiles, they are not aligned with the clinical protocols of the Ministry of Health of the Republic of Kazakhstan, and they do not support the Kazakh language.

## 4. Target users

| User group | Need | How the platform serves it |
|---|---|---|
| Parents and guardians of children aged 0–14 | Understand whether the observed symptoms require a doctor, and how urgently | Structured symptom intake, ranked likely conditions, explicit urgency level and red-flag warnings |
| Pediatricians | Prioritise incoming cases and arrive at the appointment with prior context | Doctor dashboard with triaged requests and the child's check history |
| Clinics | Reduce the share of non-urgent visits and route urgent ones faster | Triage and appointment routing by urgency |

## 5. Aim

To design and implement an intelligent web and mobile platform that performs a preliminary, evidence-based assessment of childhood disease symptoms, communicates the required urgency of medical attention, and routes complex cases to a pediatrician — without replacing clinical diagnosis.

## 6. Objectives

1. Collect and document user requirements through interviews with pediatricians and parents, and derive functional and non-functional requirements with verifiable acceptance criteria.
2. Review existing research on symptom-based preliminary diagnosis and machine learning methods applicable to pediatric data, and identify the limitations this project must account for.
3. Compile a structured symptom–condition knowledge base for the most frequent childhood diseases, aligned with the clinical protocols of the Ministry of Health of the Republic of Kazakhstan.
4. Design and train a classification model that ranks likely conditions from a reported symptom set, child age and weight, and evaluate it against a documented baseline.
5. Implement a triage component that assigns an urgency level and surfaces red-flag symptoms requiring emergency care.
6. Implement the user-facing application (symptom intake, results, check history, child profile) with a Russian, Kazakh and English interface.
7. Implement the pediatrician dashboard for reviewing and prioritising incoming requests.
8. Verify the system against the defined acceptance criteria and evaluate model quality on a held-out test set.

## 7. Expected outcomes

| Outcome | Form of delivery |
|---|---|
| Working platform | Web application with a responsive mobile interface |
| Classification model | Trained model with documented metrics and a reproducible training pipeline |
| Triage component | Rule set for urgency levels and red-flag detection, derived from clinical protocols |
| Knowledge base | Structured symptom–condition dataset covering the targeted conditions |
| Doctor dashboard | Request queue with triage, history and status tracking |
| Project documentation | Requirements, architecture, diagrams, verification and evaluation plan |
| Evaluation report | Model metrics against the baseline and results of acceptance testing |

**Target quality indicators** (to be confirmed after the first evaluation round):

- Top-3 accuracy of the ranked condition list — not lower than 75 % on the test set
- Recall for cases classified as high urgency — not lower than 90 %, since a missed urgent case is the costliest error in this domain
- Median response time of the assessment — under 3 seconds

## 8. Project scope

**In scope**

- Preliminary assessment for the most frequent childhood conditions (respiratory, gastrointestinal, ENT and common skin conditions) in children aged 0–14
- Symptom intake with duration, child age and weight
- Ranked list of likely conditions with an explanation of which symptoms match
- Urgency classification and red-flag warnings
- Check history per child profile
- Pediatrician dashboard with a triaged request queue
- Interface in Russian, Kazakh and English

**Out of scope**

- Final medical diagnosis and prescription of treatment — the platform states explicitly that it does not replace a physician
- Image-based diagnosis (rash photographs, X-rays, laboratory result interpretation)
- Integration with state medical information systems and electronic health records
- Prescription of medication or dosage calculation
- Emergency dispatch — the platform directs the user to call 103 but does not place the call
- Conditions outside the selected groups, and patients over 14 years old

## 9. Engineering complexity

The project is not a static informational site. It requires a classification model trained on a symptom–condition dataset; a separately engineered triage layer, because urgency and likelihood are different problems and the cost of error is asymmetric; a multilingual interface across three languages with differing medical terminology; a role-based system with distinct parent and physician workflows; and handling of children's health data, which imposes requirements on storage, access control and consent.

## 10. Known risks

| Risk | Impact | Mitigation |
|---|---|---|
| No open pediatric symptom dataset for the local context | High — model cannot be trained | Build the knowledge base from clinical protocols; validate with the supervisor and interviewed pediatricians |
| False negative on an urgent case | Critical — harm to a child | Tune the triage layer for recall over precision; always display red-flag warnings regardless of the ranked result |
| Users treat the output as a diagnosis | High — self-medication | Persistent disclaimer in the interface; wording focused on urgency of seeing a doctor rather than on naming a disease |
| Limited access to pediatricians for interviews | Medium — weak requirements evidence | Start scheduling interviews in the first week; supplement with parent interviews |
| Three-person team with parallel coursework | Medium — missed deadlines | Tasks tracked in YouTrack with owners and deadlines; weekly supervisor check-ins |

## 11. References to other project documents

- `docs/02-relevance-evidence.md` — interviews and evidence informing the requirements
- `docs/03-literature-review.md` — review of relevant research
- `docs/04-existing-systems-comparison.md` — comparison of existing systems
- `docs/05-technology-comparison.md` — technology decisions and alternatives
- `docs/06-requirements.md` — functional and non-functional requirements
- `docs/07-architecture.md` — architecture description and diagrams
- `docs/08-verification-plan.md` — verification and evaluation plan
