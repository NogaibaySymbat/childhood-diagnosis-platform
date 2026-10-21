# Requirements Specification

**Document:** 06-requirements.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Last updated:** 2026-10-20
**Status:** Draft for pre-defense 1

---

## 1. Purpose and scope of this document

This document defines the functional and non-functional requirements of the platform, their priorities, and the acceptance criteria by which each mandatory requirement will be verified. It is the reference for the verification and evaluation plan (`docs/08-verification-plan.md`) and for the architecture (`docs/07-architecture.md`).

Requirements marked **Must** are mandatory for the final defense. Every Must requirement has an acceptance criterion that can be checked by a test, a measurement, or a documented inspection.

## 2. Prioritisation scheme

| Priority | Meaning |
|---|---|
| **Must** | The project is not complete without it. Verified before the final defense. |
| **Should** | Significant value; implemented if the schedule allows. |
| **Could** | Desirable; implemented only after all Must and Should items are done. |
| **Won't** | Explicitly out of scope for this project (see `docs/01-proposal.md`, section 8). |

## 3. Actors

| Actor | Description |
|---|---|
| **Parent** | A parent or guardian of a child aged 0–14 who reports symptoms and receives a preliminary assessment. |
| **Pediatrician** | A physician who reviews triaged requests and responds to parents. |
| **System** | The assessment and triage components acting without direct user input. |
| **Administrator** | Maintains the symptom–condition knowledge base and user accounts. |

## 4. Functional requirements

### 4.1 Child profile management

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-01 | A parent can create a child profile with name, date of birth, sex and weight. | Must | A profile is created with all four fields; it is retrievable after the session ends; invalid dates of birth (future dates, age over 14) are rejected with an explanatory message. |
| FR-02 | A parent can maintain several child profiles and switch the active profile. | Must | At least two profiles can coexist under one account; a check is recorded against the profile that was active when it was started. |
| FR-03 | A parent can record known allergies for a child. | Should | Recorded allergies are shown on the result screen and in the request sent to a pediatrician. |
| FR-04 | A parent can edit or delete a child profile and all its data. | Must | After deletion, neither the profile nor its check history is returned by any query for that account. |

### 4.2 Symptom intake

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-05 | A parent can select symptoms from a predefined catalogue. | Must | The catalogue covers all symptoms used by the conditions in the knowledge base; a selected symptom appears in the current check and can be removed. |
| FR-06 | A parent can find a symptom by typing part of its name. | Must | Searching a substring of at least two characters returns all matching catalogue entries in Russian, Kazakh and English. |
| FR-07 | A parent can specify the duration of the symptoms. | Must | Duration is a required field; an assessment cannot be submitted without it. |
| FR-08 | A parent can enter a measured body temperature. | Must | Values are accepted in the range 34.0–42.0 °C; values outside the range are rejected with an explanatory message. |
| FR-09 | The system refuses to run an assessment on an empty symptom set. | Must | Submitting with zero selected symptoms produces a validation message and no assessment record. |

### 4.3 Preliminary assessment

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-10 | The system returns a ranked list of likely conditions for the reported symptom set, child age and weight. | Must | For every valid submission the system returns between one and five conditions, each with a confidence value, ordered by descending confidence. |
| FR-11 | The system shows, for each listed condition, which reported symptoms support it and which expected symptoms are absent. | Must | Each returned condition displays both groups; symptoms shown as matching are a subset of those actually reported. |
| FR-12 | The system assigns one of four urgency levels: emergency, within 24 hours, scheduled appointment, home observation. | Must | Every assessment returns exactly one urgency level; the level is determined by the triage rules documented in `docs/07-architecture.md`. |
| FR-13 | The system displays red-flag symptoms that require emergency care, independently of the ranked conditions. | Must | The red-flag block is present on every result, including results whose urgency level is home observation. |
| FR-14 | The system states on every result that the assessment is not a medical diagnosis. | Must | The disclaimer is visible on the result screen without scrolling, at both desktop and 390 px mobile width. |
| FR-15 | The system gives home-care recommendations appropriate to the urgency level and the child's weight. | Should | Recommendations are shown for urgency levels other than emergency; no recommendation names a specific medication or dosage. |

### 4.4 History

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-16 | The system stores every completed check in the active child's history. | Must | After a check, the record appears in that child's history with its date, reported symptoms, ranked result and urgency level. |
| FR-17 | A parent can open a past check and see its full result. | Must | The stored result is identical to the one originally shown. |
| FR-18 | A parent can export a child's history for an appointment. | Could | The exported document contains the check dates, symptoms and assessments for the selected child. |

### 4.5 Pediatrician workflow

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-19 | A parent can send a completed check to a pediatrician. | Must | The request appears in the pediatrician's queue with the check attached. |
| FR-20 | A pediatrician sees incoming requests ordered by urgency, then by time of arrival. | Must | Given requests of mixed urgency, the queue lists all emergency and within-24-hours requests above the rest. |
| FR-21 | A pediatrician can open a request and see the child's age, weight, reported symptoms, the system's assessment, and prior checks. | Must | All five elements are present on the request view. |
| FR-22 | A pediatrician can reply to the parent in a message thread. | Must | A reply is delivered to the parent and stored with the request. |
| FR-23 | A pediatrician can mark a request as handled. | Must | A handled request leaves the active queue and remains retrievable in the archive. |

### 4.6 Accounts and languages

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-24 | A user can register, sign in and sign out. | Must | Credentials are verified on sign-in; after sign-out no protected resource is accessible with the previous session. |
| FR-25 | Parent and pediatrician accounts have distinct permissions. | Must | A parent account receives an authorisation error on every pediatrician endpoint, and vice versa. |
| FR-26 | The interface is available in Russian, Kazakh and English. | Must | Every screen renders fully in all three languages with no untranslated strings; medical terms are reviewed by the supervisor. |
| FR-27 | An administrator can add and edit entries in the symptom–condition knowledge base. | Should | A change to the knowledge base takes effect on subsequent assessments without a redeployment. |

## 5. Non-functional requirements

| ID | Category | Requirement | Priority | Acceptance criteria |
|---|---|---|---|---|
| NFR-01 | Performance | An assessment returns a result quickly enough for interactive use. | Must | Median response time under 3 seconds and 95th percentile under 5 seconds, measured over 100 consecutive assessments under the conditions stated in `docs/08-verification-plan.md`. |
| NFR-02 | Model quality | The ranked list contains the reference condition. | Must | Top-3 accuracy not lower than 75 % on a held-out test set, measured against the baseline defined in the verification plan. |
| NFR-03 | Safety | Urgent cases are not misclassified as non-urgent. | Must | Recall for the emergency and within-24-hours classes combined not lower than 90 % on the held-out test set. This takes precedence over NFR-02: a loss of ranking accuracy is acceptable if it raises urgent-case recall. |
| NFR-04 | Usability | A first-time user completes a check without instruction. | Should | In a usability session with at least five parents, at least four complete a full check unaided in under 3 minutes. |
| NFR-05 | Compatibility | The interface works on mobile and desktop. | Must | All screens are usable without horizontal scrolling from 360 px to 1920 px width, in the current versions of Chrome, Safari and Firefox. |
| NFR-06 | Security | Credentials are never stored in recoverable form. | Must | Passwords are stored only as salted hashes; no credential, token or key is present in the repository or in logs. |
| NFR-07 | Security | Health data is transmitted and stored protected. | Must | All traffic uses HTTPS; the database stores child health records encrypted at rest. |
| NFR-08 | Privacy | A parent can delete all data about a child. | Must | Deletion removes the profile, its checks and its messages; verified by querying the database directly after deletion. |
| NFR-09 | Privacy | Only the assigned pediatrician and the child's own parent can read a request. | Must | A request opened by any other account returns an authorisation error. |
| NFR-10 | Reliability | A failure of the model component does not leave the user without guidance. | Should | If the model is unavailable, the system still returns the triage urgency level and the red-flag block, with a notice that the ranked list is unavailable. |
| NFR-11 | Maintainability | The knowledge base is separate from application code. | Should | A condition can be added to the knowledge base without changing application source files. |
| NFR-12 | Traceability | Project work is traceable from task to change. | Must | Every merge request references a YouTrack task; every task has an owner, acceptance criteria and a deadline. |

## 6. Constraints and assumptions

**Constraints**

- The assessment logic must follow the clinical protocols of the Ministry of Health of the Republic of Kazakhstan; where no protocol covers a case, the system must not produce an assessment.
- The platform targets children aged 0–14 only.
- Development is carried out by three students alongside other coursework, within the diploma project timeline.

**Assumptions**

- Parents have a smartphone or computer with internet access.
- Parents can measure body temperature and report symptoms in everyday language.
- The supervisor is available to review medical content and terminology.

**Open issues**

- The target values in NFR-01 to NFR-03 are provisional and will be confirmed after the first evaluation round and agreed with the supervisor.
- The exact set of conditions covered by the knowledge base is being finalised in `docs/03-literature-review.md`.

## 7. Requirement sources

Requirements in this document are derived from the interviews and observations recorded in `docs/02-relevance-evidence.md`, from the clinical protocols referenced there, and from the analysis of existing systems in `docs/04-existing-systems-comparison.md`. Each requirement traceable to a specific interview is marked in that document; requirements not yet backed by collected evidence are treated as assumptions until confirmed.
