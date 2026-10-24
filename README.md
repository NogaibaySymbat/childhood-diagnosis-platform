# Childhood Diagnosis Platform

**Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases**
Разработка интеллектуальной платформы предварительной диагностики детских заболеваний
Балалар ауруларын алдын ала диагностикалаудың зияткерлік платформасын әзірлеу

Diploma project, Astana IT University, School of Software Engineering, 2026/2027.

---

## Problem

Parents of young children cannot reliably tell a self-limiting viral infection from a condition that needs same-day medical attention. This leads to delayed treatment, self-medication including unnecessary antibiotics, and a large share of non-urgent visits competing with urgent ones in pediatric clinics.

Existing symptom checkers do not solve this locally: they are built around adult symptom profiles, are not aligned with the clinical protocols of the Ministry of Health of the Republic of Kazakhstan, and do not support the Kazakh language.

## What the platform does

- Collects a structured symptom set together with the child's age and weight
- Returns a ranked list of likely conditions with the matching and missing symptoms for each
- Assigns an urgency level through a separate deterministic triage layer, and always displays red-flag symptoms requiring emergency care
- Routes complex cases to a pediatrician, who sees a prioritised queue and the child's check history
- Works in Russian, Kazakh and English

The platform performs a **preliminary assessment only**. It does not make a diagnosis, does not prescribe treatment, and does not replace a physician.

## Target users

| User group | What they get |
|---|---|
| Parents and guardians of children aged 0–14 | Structured intake, ranked assessment, urgency level and red-flag warnings |
| Pediatricians | Triaged request queue with prior context for each child |
| Clinics | Fewer non-urgent visits, faster routing of urgent ones |

## Expected outcomes

- Working web application with a responsive mobile interface
- Classification model with documented metrics and a reproducible training pipeline
- Deterministic triage component derived from clinical protocols
- Structured symptom–condition knowledge base
- Pediatrician dashboard
- Full project documentation and an evaluation report

Target quality indicators (provisional, to be confirmed after the first evaluation round):
top-3 accuracy ≥ 75 %, recall for urgent cases ≥ 90 %, median response time < 3 s.

## Scope

**In scope:** respiratory, gastrointestinal, ENT and common skin conditions in children aged 0–14; symptom intake; ranked assessment; urgency classification; check history; pediatrician dashboard; three-language interface.

**Out of scope:** final diagnosis and treatment; image-based diagnosis; integration with state medical information systems; medication dosage; emergency dispatch; patients over 14.

## Team

| Name | Group | Responsibility |
|---|---|---|
| Nogaibay Symbat | SE-2402 | Backend, repository and project structure, literature review |
| Sagynbaeva Zhanat | SE-2402 | Machine learning model, architecture, technology comparison |
| Sabyr Aruzhan | SE-2402 | Requirements, user research, frontend and UX |

**Supervisor:** Yermaganbetova Madina Askarovna

## Technologies

*Selected options; the comparison of alternatives and the rationale are in [`docs/05-technology-comparison.md`](docs/05-technology-comparison.md).*

| Layer | Choice |
|---|---|
| Backend | FastAPI (Python) |
| Data storage | PostgreSQL |
| Model | Knowledge base + gradient boosting (hybrid) |
| Frontend | React |
| Component interaction | Modular monolith with an internal model service boundary |

## Current status

Documentation, requirements and architecture. No runnable application yet; setup and testing instructions will be added here as soon as code is available.

## Documentation

| Document | Contents |
|---|---|
| [`docs/01-proposal.md`](docs/01-proposal.md) | Problem, users, aim, objectives, scope, risks |
| [`docs/02-relevance-evidence.md`](docs/02-relevance-evidence.md) | Interviews and evidence behind the requirements |
| [`docs/03-literature-review.md`](docs/03-literature-review.md) | Review of relevant research |
| [`docs/04-existing-systems-comparison.md`](docs/04-existing-systems-comparison.md) | Comparison of existing systems |
| [`docs/05-technology-comparison.md`](docs/05-technology-comparison.md) | Technology decisions and alternatives |
| [`docs/06-requirements.md`](docs/06-requirements.md) | Functional and non-functional requirements |
| [`docs/07-architecture.md`](docs/07-architecture.md) | Architecture description and diagrams |
| [`docs/08-verification-plan.md`](docs/08-verification-plan.md) | Verification and evaluation plan |
| [`docs/09-risks-and-management.md`](docs/09-risks-and-management.md) | Roles, milestones, risks |
| [`docs/10-supervisor-feedback.md`](docs/10-supervisor-feedback.md) | Supervisor feedback and resulting changes |

https://powerpuff.youtrack.cloud/projects/DIP


## Repository structure

```
docs/         project documentation
diagrams/src  editable diagram sources
diagrams/export  rendered diagrams
prototype/    UI prototype and screenshots
src/          application code
tests/        automated tests
config/       configuration examples
```

## Setup

Not yet applicable — there is no runnable code at this stage.

When the application exists, copy `config/.env.example` to `.env` and fill in real values. `.env` is ignored by git; no credential, key or personal data is ever committed to this repository.

## Contributing workflow

`main` is the reviewed baseline. All work goes through task branches and pull requests.

```
git checkout main && git pull
git checkout -b DIP-<task-id>-<short-name>
# make changes
git commit -m "DIP-<task-id>: <what changed>"
git push -u origin DIP-<task-id>-<short-name>
# open a pull request, request review from another team member
```

Each pull request references its YouTrack task and is reviewed by another team member before merging. Submissions for each pre-defense stage are tagged: `pre-defense-1`, `pre-defense-2`, `final-pre-defense`.

## Disclaimer

This software provides preliminary information only and does not constitute medical advice, diagnosis or treatment. In an emergency, call 103.
