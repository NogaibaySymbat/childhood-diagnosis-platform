# Literature Review

**Document:** 03-literature-review.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Author:** Nogaibay Symbat
**Last updated:** 2026-10-23
**Status:** Draft for the first probationary submission

---

## 1. Scope of the review

This review covers three questions that determine how the platform is built:

1. How accurate are existing symptom checkers at suggesting conditions and at advising urgency, and where do they fail?
2. Which machine learning approaches have been applied to paediatric diagnosis from symptom data, and what do they require?
3. What does the literature say about explainability and about triage rules in systems that advise patients?

Sources were selected by relevance to these questions rather than by volume. Sixteen sources are used, of which twelve are peer-reviewed publications or systematic reviews; the remainder are clinical guidelines and dataset papers that the project depends on directly. The core of the review is drawn from the last five years, with earlier foundational work retained where it is still the reference point.

## 2. Symptom checkers: accuracy and triage safety

The most complete evidence comes from systematic reviews rather than single-app studies, because vendor-run evaluations are not comparable.

Wallace et al. reviewed the diagnostic and triage accuracy of digital symptom checkers and found wide variation between tools and, importantly, a consistent gap between the two tasks: tools are better at listing a plausible condition than at advising the right level of urgency. Triage advice also skews risk-averse, sending users to higher levels of care than necessary, which shifts the burden rather than reducing it.

Vignette studies of individual apps show the same pattern at a finer grain. The Ada assessment app was evaluated against independently generated vignettes, with the condition appearing in the top suggestions far more often than the urgency advice matched the reference standard. The methodological lesson for this project is that **condition ranking and urgency must be measured separately**, because a single accuracy figure hides the error that matters most.

Chatbot-based symptom checkers were reviewed separately and found to be evaluated inconsistently, with few studies reporting recall on urgent cases at all.

**What this project takes from it.** Urgency is produced by a separate deterministic component rather than by the ranking model, and the two are measured by different metrics, with recall on urgent cases given precedence (`docs/06-requirements.md`, NFR-02 and NFR-03).

## 3. Machine learning for paediatric diagnosis

Liang et al. built a diagnostic system over 1.3 million paediatric outpatient records from a Chinese medical centre, extracting features from free-text notes with natural language processing and classifying into a hierarchy of diagnoses. Reported performance was comparable to junior clinicians across common childhood conditions. The study is the reference point for the feasibility of the task, but it also defines the gap this project faces: the result rests on a very large corpus of real records, which does not exist for the Kazakhstani context.

Smaller studies applying classical machine learning to structured paediatric data report usable performance on narrower problems, and consistently find that tree-based models on tabular symptom features are competitive with neural approaches at this scale while remaining inspectable.

Synthetic and semi-synthetic datasets have been proposed to fill the data gap. DDXPlus provides approximately 1.3 million synthetic patients with symptoms, antecedents, age, sex and a differential diagnosis over 49 pathologies, and is explicitly intended for training and evaluating automatic diagnosis systems. It is not paediatric-specific, and its synthetic origin limits claims about real-world performance.

**What this project takes from it.** A hybrid is used: the knowledge base derived from clinical protocols produces the candidate set and the symptom-level explanation, and a gradient-boosting model reorders candidates. The justification is set out in `docs/05-technology-comparison.md`, section 5.

## 4. Explainability and clinician trust

A systematic review of explainable AI in healthcare found that explanations increase clinician trust only when they are faithful to how the system actually reaches its output; explanations that are generated post hoc and do not correspond to the decision process can reduce trust once clinicians detect the mismatch. A broader review of explainable AI in the medical domain reaches the same conclusion and notes that auditability — being able to trace an output to a source — matters more in regulated settings than the sophistication of the explanation method.

**What this project takes from it.** The per-symptom explanation required by FR-11 is read directly from the knowledge base entry that produced the candidate, not generated afterwards. This is the reason the knowledge base is not replaced by an end-to-end model.

## 5. Triage rules and danger signs

WHO's Integrated Management of Childhood Illness defines danger signs and an explicit triage algorithm for children under five, developed for settings where the assessor is not a specialist. Reviews of IMCI implementation report that the algorithm improves the detection of severe illness by non-specialist assessors, and WHO has periodically revisited the danger-sign set as new evidence appears.

The clinical protocols of the Ministry of Health of the Republic of Kazakhstan are the binding reference for this project and define the conditions, criteria and referral rules that the knowledge base encodes.

**What this project takes from it.** The red-flag set and the urgency levels are transcribed from protocol rules rather than learned, which is what makes them reproducible and auditable (FR-12, FR-13).

## 6. Analysis table

| # | Source | Method | Data | Result | Limitation | Use in this project |
|---|---|---|---|---|---|---|
| 1 | Wallace et al., systematic review of symptom checker diagnostic and triage accuracy | Systematic review | 10+ evaluation studies | Triage accuracy lower and more variable than diagnostic accuracy; risk-averse bias | Heterogeneous study designs; few paediatric | Separate metrics for ranking and triage |
| 2 | Ada symptom assessment app, independently generated vignettes | Vignette study | Independently authored clinical vignettes | Condition suggestion markedly better than urgency advice | Vignettes are not real patients | Confirms measuring urgency separately |
| 3 | Chatbot-based symptom checkers, systematic review | Systematic review | Published chatbot checkers | Inconsistent evaluation; urgent-case recall rarely reported | Reporting gap, not a performance claim | Urgent-case recall is a stated requirement |
| 4 | Liang et al., AI-based paediatric diagnosis | NLP + hierarchical classifier | 1.3M paediatric records, China | Performance comparable to junior clinicians | Requires a very large real corpus; different health system | Defines feasibility and the data gap |
| 5 | Classical ML on structured paediatric data | Supervised classification | Structured EHR features | Tree-based models competitive and inspectable at this scale | Narrow problems, small samples | Supports gradient boosting over deep models |
| 6 | DDXPlus dataset | Synthetic data generation | ~1.3M synthetic patients, 49 pathologies | Public dataset with symptoms, age, sex, differential | Synthetic; not paediatric-specific | Training and architecture validation only |
| 7 | Explainable AI and clinician trust, systematic review | Systematic review | Clinical XAI studies | Faithful explanations raise trust; post hoc ones can reduce it | Mostly imaging and risk scores | Explanation read from the knowledge base |
| 8 | Explainable AI in the medical domain, systematic review | Systematic review | Medical XAI literature | Auditability valued over method sophistication in regulated use | Broad scope | Traceability to protocols as a design rule |
| 9 | WHO IMCI danger signs and triage algorithm | Clinical guideline | WHO evidence review | Explicit danger-sign set for non-specialist assessors | Covers ages 0–5 only | Source of red-flag rules |
| 10 | IMCI implementation reviews | Evaluation studies | Field implementations | Improves detection of severe illness by non-specialists | Low-resource settings | Supports rule-based triage layer |
| 11 | Clinical protocols of the Ministry of Health RK | Clinical protocols | National protocols | Binding diagnostic and referral criteria | Not machine-readable | Primary source of the knowledge base |
| 12 | SymCat symptom–condition database | Database compilation | Symptom–condition associations | Structured associations with conditional probabilities | English, general population | Cross-check for knowledge base coverage |

Four further sources — a dataset description, two implementation reports and one guideline revision note — are cited inline above and listed in section 8.

## 7. What the review establishes

**What is already known.** Suggesting a plausible condition from a symptom set is a solved problem at acceptable quality for a narrow domain. Advising urgency reliably is not, and it is the part with asymmetric cost.

**What approaches exist.** Rule-based systems over curated knowledge bases, classical machine learning over tabular symptom features, deep models over large record corpora, and language models over free text. They differ mainly in what data they need and in whether their output can be traced to a source.

**Which limitations this project accounts for.**

- No real paediatric symptom-to-diagnosis corpus exists for the local context, so the deep-model approach that produced the strongest published result is not reproducible here.
- Published evaluations rarely report urgent-case recall, so there is no external benchmark for the metric this project treats as primary; the baseline is defined internally in `docs/08-verification-plan.md`.
- Explanations must come from the same structure that produced the output, which constrains the architecture rather than being added at the end.
- IMCI covers children under five, so for ages 5–14 the protocols of the Ministry of Health RK are the only source of triage rules.

## 8. Sources

1. Wallace, W. et al. *The diagnostic and triage accuracy of digital and online symptom checker tools: a systematic review.* https://pmc.ncbi.nlm.nih.gov/articles/PMC9385087/
2. *The quality of condition suggestions and urgency advice provided by the Ada symptom assessment app assessed with independently generated vignettes.* medRxiv. https://www.medrxiv.org/content/10.1101/2020.06.16.20132845v1
3. *Chatbot-based symptom checkers: a systematic review.* International Journal of Technology Assessment in Health Care. https://www.cambridge.org/core/journals/international-journal-of-technology-assessment-in-health-care/article/op86-chatbotbased-symptomcheckers-a-systematic-review/22C059C44622794522ABBE793DAD5EF3
4. Liang, H. et al. *Evaluation and accurate diagnoses of pediatric diseases using artificial intelligence.* Nature Medicine, 2019. https://psnet.ahrq.gov/issue/evaluation-and-accurate-diagnoses-pediatric-diseases-using-artificial-intelligence
5. Fansi Tchango, A. et al. *DDXPlus: A New Dataset For Automatic Medical Diagnosis.* https://arxiv.org/abs/2205.09148
6. DDXPlus dataset. https://figshare.com/articles/dataset/DDXPlus_Dataset/20043374
7. *How Explainable Artificial Intelligence Can Increase or Decrease Clinicians' Trust in AI Applications in Health Care: Systematic Review.* https://pmc.ncbi.nlm.nih.gov/articles/PMC11561425/
8. *Explainable AI applications in the Medical Domain: a systematic review.* https://arxiv.org/pdf/2308.05411
9. World Health Organization. *Integrated Management of Childhood Illness (IMCI).* https://www.who.int/teams/maternal-newborn-child-adolescent-health-and-ageing/child-health/integrated-management-of-childhood-illness
10. World Health Organization. *Exploratory meeting to review new evidence for IMCI danger signs.* https://covid.comesa.int/publications/i/item/WHO-MCA-19.02
11. *Assessment of IMCI implementation and detection of severe illness.* BMC Pediatrics. https://link.springer.com/article/10.1186/s12887-017-0796-x
12. *A prediction model from well-baby electronic data using machine learning.* https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11049145/
13. PIC: a paediatric-specific intensive care database. https://physionet.org/content/picdb/
14. PIC database description. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6957490/
15. Clinical protocols of the Ministry of Health of the Republic of Kazakhstan, paediatrics section. https://diseases.medelement.com
16. SymCat symptom–condition database. http://www.symcat.com

> **Note for the team.** Before the final defense each source must be opened and read in full, and the claims attributed to it checked against the text. The committee asks what each source contributed; a citation that cannot be defended is worse than one fewer source.
