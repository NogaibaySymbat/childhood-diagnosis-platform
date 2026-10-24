# Relevance Evidence

**Document:** 02-relevance-evidence.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Last updated:** 2026-10-24
**Status:** In progress — interviews being collected

---

## 1. Why this section exists

The requirements in `docs/06-requirements.md` must rest on observed need rather than assumption. This section records the method used to collect evidence, what has been established, and — explicitly — which statements remain assumptions until confirmed by a second independent participant.

## 2. Method

Semi-structured interviews with two groups: parents of children aged 0–14, for what they actually do when a child falls ill; and pediatricians, for the professional view of triage and of the errors parents make.

Interviews are conducted asynchronously through messaging, in Russian or Kazakh. Participants are told the purpose of the conversation and that their answers are used in anonymised form in a student project. No names, contact details or health data of specific children are recorded in the project repository. Responses are summarised; wording is preserved where it carries the point.

A finding is treated as **verified** when stated independently by at least two participants, or when it is confirmed by a clinical protocol. Everything else is recorded as preliminary.

## 3. Interview guide

**Parents**

1. When your child falls ill, how do you decide whether to go to a doctor now, wait, or call an ambulance?
2. Did you look for information online? Did you trust what you found?
3. Did you give any medicine before the doctor saw the child? How did you choose it?
4. What would have helped you at that moment?

**Pediatricians** (15 questions, covering the platform concept, the context needed before any assessment, how results should be explained, referral, and the boundary between a complex case and an urgent one)

Selected questions: what must be known before assessing a result; whether every deviation should be explained to a parent; whether a complex case and an urgent case must be distinguished; whether the platform may advise medication; what to do when results look normal but the child is unwell; what the physician needs to see at the appointment.

## 4. Participants

| # | Role | Context | Date | Format |
|---|---|---|---|---|
| P1 | Parent | parent of a child in the target age range | 24 Oct 2026 | Messaging, text |
| P2 | Pediatrician | practising pediatrician, primary care | 24 Oct 2026 | Written responses, 15 questions |
| P3 | | | | |

## 5. Findings

### 5.1 From parents

| ID | Finding | Stated by | Informs |
|---|---|---|---|
| F-01 | The first action when a child falls ill is to search the internet for the symptoms; a doctor is consulted only once the parent suspects something serious | P1 | Problem statement in `docs/01-proposal.md`; FR-05 to FR-09 — structured intake exists to replace an unstructured search |
| F-02 | Online information is broadly trusted — "the internet is very developed now" — but the parent still intends to see a doctor to confirm | P1 | FR-14 — disclaimer on every result; FR-19 — routing to a pediatrician is the expected continuation |
| F-03 | Only medicines already familiar to the family are given before an examination; unfamiliar ones are not | P1 | FR-15 — home-care recommendations must not name medicines or dosages |
| F-04 | The urgency decision is made by how unwell the child looks: an ambulance if clearly very unwell, otherwise the parent follows what they read online | P1 | FR-12, FR-13 — urgency level and red flags are the core of the product, because "otherwise fine" is exactly where a serious condition is missed |

### 5.2 From pediatricians

| ID | Finding | Stated by | Informs |
|---|---|---|---|
| F-05 | Nothing can be assessed without context: the child's age, the complaints, when they started, current medication and chronic conditions. "The same result cannot be explained the same way without these circumstances" | P2 | FR-01 (age, weight), FR-07 (duration), FR-03 (allergies); **identifies a gap** — current medication and chronic conditions are not yet captured |
| F-06 | Deviations may be explained, but without categorical conclusions: a single abnormal indicator does not mean a specific disease, and nothing should turn every deviation into a reason to panic | P2 | FR-11 — show what supports and what is absent; FR-14 — the assessment is preliminary, not a diagnosis |
| F-07 | A complex case and an urgent case must be distinguished. A case may be unclear without being urgent; and when danger signs are present, nothing may wait for uploads or further data | P2 | **Direct external confirmation of the architecture**: FR-12, FR-13, NFR-03 and the separation of the triage engine from the ranking model in `docs/07-architecture.md` |
| F-08 | The platform must not advise medication or change prescribed treatment; it is more useful to say which questions to raise with the doctor and which documents to bring | P2 | FR-15 |
| F-09 | Normal results must not override a parent's report that the child is unwell: "you cannot write that everything is fine only because the values are within range" | P2 | FR-12, FR-13 — the red-flag block is produced independently of the ranked result |
| F-10 | When the situation cannot be assessed remotely, the platform should say so plainly rather than produce a conclusion for every case | P2 | Out-of-scope behaviour: an uncovered symptom set returns an explicit "cannot assess" rather than a guess (`docs/09-risks-and-management.md`, R-5) |
| F-11 | Referral should start with a pediatrician when the specialist is unclear; a family should not be sent to several specialists over one changed indicator, and the reason for the referral must be understandable to the parent | P2 | FR-19 to FR-23 — routing to a pediatrician first; the result explains why |
| F-12 | At the appointment the physician wants the collected information in one place — complaints, onset, medication — but also the original materials, not only the application's conclusion | P2 | FR-21 — the request view must show the reported data itself, not just the assessment |

### 5.3 Cross-confirmed findings

| Requirement | Supported by | Nature of the agreement |
|---|---|---|
| FR-15 — no medication or dosage advice | P1 and P2 | A parent states she gives only familiar medicines and would act on what she is told; a pediatrician states the platform must not advise medication at all. Two perspectives, same requirement |
| FR-12, FR-13 — urgency and red flags independent of the ranked result | P1 and P2 | A parent judges urgency by appearance and otherwise follows online advice; a pediatrician states that urgency must be communicated separately from how complex the case is |

## 6. Verified findings and assumptions

**Verified** — stated independently by two participants, or confirmed by a clinical protocol:

- FR-15 must not name medicines or dosages (P1, P2).
- Urgency must be communicated separately from the ranked assessment (P1, P2; also required by the WHO IMCI and MoH RK protocols, which define danger signs independently of a differential diagnosis).

**Preliminary** — stated by one participant:

- F-01, F-02, F-04 (parent behaviour) — one parent.
- F-05 to F-12 (clinical requirements) — one pediatrician.

**Assumptions** — carried over from the initial project idea and not yet confirmed:

- Parents are willing to enter a structured symptom set rather than describe the problem in free text.
- A three-language interface is required rather than Russian only.
- Pediatricians would use a triaged request queue rather than their existing workflow.

## 7. Scope question raised by the P2 interview

The pediatrician interview was conducted around a broader platform concept — one that also interprets laboratory results and photographs of visible symptoms. The current scope (`docs/01-proposal.md`, section 8) explicitly excludes both: image-based assessment and laboratory result interpretation are out of scope for this project.

This is recorded rather than quietly resolved. Two of P2's findings relate to that wider concept (what must be visible in an uploaded result form; when a photograph adds information), and they are not used to justify any current requirement. The remaining findings — F-05 to F-12 — apply to the symptom-based assessment as specified and are used.

**Open question for the supervisor:** whether laboratory result interpretation and photograph intake should enter the scope at a later stage. Added to the list in `docs/10-supervisor-feedback.md`, section 4.

## 8. Supporting sources

Independently of the interviews, two documented sources support the problem statement:

- The systematic reviews summarised in `docs/03-literature-review.md`, which establish that existing symptom checkers advise urgency less reliably than they suggest conditions.
- The clinical protocols of the Ministry of Health of the Republic of Kazakhstan and WHO IMCI, which define the danger signs and referral criteria the triage layer encodes.

## 9. Effect on the project

The pediatrician interview confirmed the project's central design decision from the outside. F-07 — a case may be complex without being urgent, and danger signs must not wait for further data — is exactly the separation of the triage engine from the ranking model described in `docs/07-architecture.md`, section 5. Before this interview that separation rested on the literature and on the team's reasoning; it now also rests on a practitioner's statement.

Three findings change the requirements rather than confirm them:

1. **F-05 identifies a gap.** Current medication and chronic conditions are not captured anywhere in `docs/06-requirements.md`. A pediatrician states that a result cannot be interpreted without them. Candidate requirements to be added before the next stage.
2. **F-10 strengthens the "cannot assess" path.** It is currently a risk mitigation (R-5); on the strength of this interview it should become an explicit functional requirement.
3. **F-12 changes the pediatrician's view.** The request screen must show the reported data itself, not only the system's conclusion, so that the physician can form their own judgement.

Interviews continue. Any assumption still unconfirmed at the final defense is reported as a limitation in the evaluation report rather than quietly dropped.
