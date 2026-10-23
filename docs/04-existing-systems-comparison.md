# Existing Systems Comparison

**Document:** 04-existing-systems-comparison.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Author:** Nogaibay Symbat
**Last updated:** 2026-10-23
**Status:** Draft for the first probationary submission

---

## 1. Purpose and selection

This document compares the systems a parent in Kazakhstan could realistically use today when a child falls ill, against a consistent set of criteria, and states what gap the proposed platform fills.

Five systems were selected to cover the distinct categories available:

| System | Category | Why included |
|---|---|---|
| Ada Health | Dedicated symptom assessment app | The most studied symptom checker, with published vignette evaluations |
| Symptomate (Infermedica) | Symptom checker with a clinical knowledge base | Rule- and probability-based, used by health providers |
| Babylon Health | Symptom checker inside a telemedicine service | Combines triage with routing to a clinician |
| Isabel | Differential diagnosis tool for clinicians | Represents the professional, not consumer, end of the field |
| General-purpose AI assistants (ChatGPT and similar) | Conversational language model | What parents actually reach for in practice |

Systems were assessed from published documentation, published evaluations and the products' own public interfaces. Where a claim could not be supported by a source it is marked as not established rather than asserted.

## 2. Comparison criteria

Nine criteria were chosen because each maps to a requirement in `docs/06-requirements.md` or to a constraint the project must satisfy.

| # | Criterion | Why it matters | Related requirement |
|---|---|---|---|
| C1 | Paediatric coverage | Symptom profiles and danger signs differ by age and weight | FR-01, FR-10 |
| C2 | Source of knowledge | Determines whether an output can be traced and defended | NFR-11 |
| C3 | Separate triage layer | Urgency must not depend on a probabilistic ranking | FR-12, NFR-03 |
| C4 | Explainability of the result | The user must see which symptoms support a suggestion | FR-11 |
| C5 | Published quality metrics | Without them, quality claims are unverifiable | NFR-02 |
| C6 | Kazakh language | Required for the target users | FR-26 |
| C7 | Alignment with MoH RK protocols | Binding for clinical content in Kazakhstan | Constraint |
| C8 | Physician in the loop | Complex cases must reach a clinician | FR-19 to FR-23 |
| C9 | Handling of children's personal data | Legal requirement for health data of minors | NFR-07, NFR-08 |

## 3. Comparison

| Criterion | Ada Health | Symptomate | Babylon | Isabel | General AI assistants | **This project** |
|---|---|---|---|---|---|---|
| **C1** Paediatric coverage | Partial; adult-oriented profile with limited paediatric handling | Partial | Partial, adult-oriented | Includes paediatric differential, built for clinicians | None specific | **Primary focus, ages 0–14, age and weight as inputs** |
| **C2** Source of knowledge | Proprietary medical knowledge base, not public | Proprietary knowledge base with published methodology | Proprietary | Curated clinical database | Statistical, not attributable to a source | **Knowledge base built from MoH RK protocols, open to inspection within the project** |
| **C3** Separate triage layer | Urgency advice produced alongside condition suggestion | Triage levels defined in the knowledge base | Triage integrated with routing | Not a triage tool | None; urgency is part of generated text | **Deterministic rules, independent of the ranking model** |
| **C4** Explainability | Shows questions asked, not per-symptom support | Shows supporting and opposing evidence | Limited | Shows matching clinical features | Explanation is generated text, not traceable | **Matching and absent symptoms per condition, read from the knowledge base** |
| **C5** Published quality metrics | Yes — independent vignette studies published | Partially; vendor-published evaluations | Disputed in published literature | Published evaluations for clinician use | None for this task | **Targets stated and measured against a baseline before the final defense** |
| **C6** Kazakh language | No | No | No | No | Partial, uneven for medical terminology | **Yes, alongside Russian and English** |
| **C7** MoH RK protocols | No | No | No | No | No | **Yes, primary source** |
| **C8** Physician in the loop | No; refers the user onward | No | Yes, within its own telemedicine service | Used by clinicians directly | No | **Yes, triaged queue for a pediatrician** |
| **C9** Children's data handling | General privacy policy, not specific to minors in Kazakhstan | General | General | Clinician-facing | Not designed for health data | **Deletion of a child's record, encryption at rest, access limited to the parent and the assigned pediatrician** |

## 4. Reading of the comparison

**Where existing systems are strong.** Ada and Symptomate are built on substantial curated medical knowledge and have been evaluated more rigorously than anything this project will produce within a diploma timeline. Isabel demonstrates that a curated database remains competitive for differential diagnosis. None of this is disputed, and the project does not claim to outperform them on general diagnostic breadth.

**Where they do not serve the target user.** All of them are built for an adult user in a different health system. None are aligned with the clinical protocols of the Republic of Kazakhstan, none support Kazakh, and none route a complex case to a pediatrician within the local primary care system. For a parent in Astana, the output of these systems is advice that cannot be acted on locally.

**Where the general AI assistants fail specifically.** They are the tools parents actually use, which makes their weaknesses the most consequential. Their output is not reproducible between sessions, cannot be traced to a clinical source, carries no measured quality, and mixes the urgency judgement into the same generated text as the content — so an urgent case can be missed silently and without any signal to the user. This is the gap the separation of ranking and triage is designed to close.

## 5. Conclusion: the gap this project fills

A preliminary assessment tool for children aged 0–14 that is aligned with the clinical protocols of the Republic of Kazakhstan, works in Kazakh, produces an urgency decision through a deterministic layer independent of the probabilistic ranking, explains its suggestions from the knowledge base rather than after the fact, and routes complex cases to a pediatrician within the local system.

No system in the comparison provides this combination. Each element exists somewhere; none are combined for this user in this context.

## 6. Limitations of this comparison

- Vendor systems were assessed from public documentation and published evaluations, not from internal specifications; some capabilities may exist but not be documented publicly.
- Published evaluations use different vignette sets and reference standards, so accuracy figures across systems are not directly comparable, and none are reproduced here as head-to-head numbers.
- The comparison reflects the state of these systems as of October 2026 and the versions available in Kazakhstan.

## 7. Sources

1. *The diagnostic and triage accuracy of digital and online symptom checker tools: a systematic review.* https://pmc.ncbi.nlm.nih.gov/articles/PMC9385087/
2. *The quality of condition suggestions and urgency advice provided by the Ada symptom assessment app.* medRxiv. https://www.medrxiv.org/content/10.1101/2020.06.16.20132845v1
3. Ada Health — how AI health assessment tools are tested. https://about.ada.com
4. Symptomate / Infermedica — product documentation. https://symptomate.com
5. Isabel Healthcare — differential diagnosis tool. https://www.isabelhealthcare.com
6. *Chatbot-based symptom checkers: a systematic review.* https://www.cambridge.org/core/journals/international-journal-of-technology-assessment-in-health-care/article/op86-chatbotbased-symptomcheckers-a-systematic-review/22C059C44622794522ABBE793DAD5EF3
7. World Health Organization. *Integrated Management of Childhood Illness.* https://www.who.int/teams/maternal-newborn-child-adolescent-health-and-ageing/child-health/integrated-management-of-childhood-illness
8. Clinical protocols of the Ministry of Health of the Republic of Kazakhstan. https://diseases.medelement.com

> **Note for the team.** Before the final defense, open each vendor's current documentation and re-check the rows marked partial or none — these products change, and a claim that was true in October may not hold at the defense.
