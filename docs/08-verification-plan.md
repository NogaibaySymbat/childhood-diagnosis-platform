# Verification and Evaluation Plan

## 1. Purpose

This plan defines how the platform's mandatory requirements and the hybrid condition-ranking approach will be checked. It separates functional verification, safety and privacy checks, model evaluation, and response-time measurement. The numerical targets are provisional project targets from the requirements and proposal; they require supervisor confirmation before they are treated as clinical evidence.

## 2. What is verified

All requirements marked Must in `docs/06-requirements.md` are in scope. Verification covers profile management, symptom intake and validation, assessment output and explanations, urgency and red flags, disclaimer visibility, check history and deletion, pediatrician requests and access control, authentication, language coverage, responsive compatibility, security and privacy, performance, ranking quality, and task traceability. Should requirements are evaluated when implemented, including graceful behavior when the ranking model is unavailable.

## 3. Outcome metrics

| Metric | Calculation | Target |
|---|---|---:|
| Top-3 accuracy | Held-out cases whose reference condition appears among the first three ranked candidates / all scored cases | ≥ 75% |
| Urgent-case recall | Emergency or within-24-hours cases correctly identified as urgent / all reference urgent cases | ≥ 90% |
| Median response time | Median end-to-end time from valid submission to complete assessment response | < 3 s |
| 95th percentile response time | 95th percentile of end-to-end response times | < 5 s |
| Must requirement pass rate | Passed Must acceptance checks / all applicable Must acceptance checks | 100% before release |

Ranking metrics are reported alongside the rule-based baseline on the same held-out cases. The urgent-case recall target takes priority over top-3 accuracy, as stated in NFR-03.

## 4. Measurement conditions

### Dataset and split

Use the documented DDXPlus dataset version for model experiments, filtering records to the project age range of 0–14 where age is available. Record the source URL or accession, version, download date, licence, filtering rules, and the number of records and classes retained. DDXPlus is synthetic and not specific to Kazakhstan or pediatric care; its results measure model behavior on that dataset only.

Create a reproducible, stratified 80/20 train/test split with a fixed random seed. Keep the test set untouched during feature selection, threshold tuning, and model selection. Where source identifiers allow it, group related or duplicate records before splitting to prevent leakage. Publish the split seed and class counts with the evaluation results. The same held-out test cases must be used for the hybrid and baseline.

### Runtime and procedure

For latency, run 100 consecutive valid assessment requests against the same documented deployment build and fixed test input set. Record hardware or hosting allocation, operating system, Python and dependency versions, database configuration, model version, request concurrency, warm-up procedure, and whether the run used a local or networked database. Report the median and 95th percentile; do not mix model-evaluation runs with latency runs. Repeat the run after any material change to the assessment path and retain the raw timings.

For acceptance checks, record the build identifier, browser and version, viewport size, test account roles, test data fixture, expected result, observed result, and pass/fail status. Use synthetic test profiles and messages. Verify access-control and deletion behavior against the database, not only the user interface.

## 5. Baseline for comparison

The baseline is a simple rule-based ranker using the same Knowledge Base candidate set. It orders candidates by the share of reported symptoms that match each condition, with a documented deterministic tie-break. It uses the same input fields, candidate coverage, and held-out test set as the hybrid system.

Report top-3 accuracy and urgent-case recall for both approaches, along with the absolute difference. The gradient-boosting hybrid is justified only if it improves the agreed ranking objective without reducing urgent-case recall below its acceptance threshold. If it does not outperform the simpler baseline, retain the rule-based ranking and report that result plainly. Triage remains a separate deterministic component for both comparisons.

## 6. Test plan

| Requirement area | How it is checked | Expected result |
|---|---|---|
| FR-01–04: child profiles | Create, retrieve, switch, and delete profiles; attempt future birth date and age over 14; inspect child history after deletion | Valid profiles persist and remain isolated; invalid ages are rejected; deleted profile and checks are no longer retrievable |
| FR-05–09: symptom intake | Select/remove catalogue symptoms; search substrings of at least two characters in all three languages; omit duration, use out-of-range temperature, submit no symptoms | Catalogue covers the Knowledge Base; search returns matching entries; invalid or empty submissions are rejected and do not create an assessment |
| FR-10–11: ranked assessment and explanation | Submit representative valid cases and inspect result count, ordering, confidence values, matching and absent symptoms | Each result contains 1–5 conditions in descending confidence; both symptom groups are present and matching symptoms were reported |
| FR-12–13: triage and red flags | Exercise fixtures for each urgency level, overlapping rules, and red flags with different ranking results | Exactly one urgency level is returned; red flags appear on every result and are independent of the ranked conditions |
| FR-14: disclaimer | Inspect result screens at desktop and 390 px mobile width | The non-diagnosis notice is visible without scrolling |
| FR-16–17: history | Complete a check, reopen it from history, and compare it with the original response | Date, symptoms, ranked result, and urgency are stored and the reopened result matches the original |
| FR-19–23: pediatrician workflow | Send a check, create requests of mixed urgency, open an assigned request, reply, close, and inspect the archive | Request includes its check; urgent requests sort first; all required context is visible; reply is stored; closed request leaves the active queue and remains archived |
| FR-24–25: accounts and roles | Register, sign in/out, access protected resources after sign-out, and call parent-only and pediatrician-only endpoints with both roles | Credentials are verified; signed-out access is denied; cross-role access returns an authorisation error |
| FR-26: language coverage | Review every screen in Russian, Kazakh, and English and check medical terminology with the supervisor | No untranslated strings remain; terminology has supervisor review |
| NFR-01: performance | Measure 100 consecutive assessments under the documented runtime conditions | Median < 3 s and p95 < 5 s |
| NFR-02: ranking quality | Run the frozen hybrid and baseline on the held-out test set | Hybrid top-3 accuracy ≥ 75%; baseline metrics and difference are reported |
| NFR-03: urgent-case safety | Compute recall for emergency and within-24-hours classes on the same held-out test set | Recall ≥ 90%; report class counts and errors; this target takes priority over NFR-02 |
| NFR-05: compatibility | Inspect all screens at widths from 360 px to 1920 px in current Chrome, Safari, and Firefox | No horizontal scrolling; controls and content remain usable |
| NFR-06–07: security | Inspect repository and logs for secrets; inspect password storage and HTTPS configuration; verify database at-rest encryption configuration | No secrets in repository or logs; passwords are salted hashes; traffic uses HTTPS; health records are stored encrypted at rest |
| NFR-08: privacy | Delete a test child's profile and query profile, checks, and messages directly | All data belonging to that child is removed |
| NFR-09: authorisation | Attempt to read a request as an unrelated parent and an unassigned pediatrician | Both attempts return an authorisation error |
| NFR-12: traceability | Inspect task records and pull requests for owner, acceptance criteria, deadline, and task reference | Every task has the specified fields and each merge request references its YouTrack task |

## 7. Acceptance thresholds

A release candidate passes this plan when every applicable Must acceptance check passes, median response time is below 3 seconds, p95 response time is below 5 seconds, held-out top-3 accuracy is at least 75%, and urgent-case recall is at least 90%. The urgent-case threshold is a safety priority: a candidate that misses it fails even if ranking accuracy passes. A model failure test must also demonstrate that the system still returns deterministic urgency and red flags when that Should behavior is implemented.

These targets are project acceptance criteria, not evidence that the platform is clinically safe for unsupervised use. The supervisor must confirm the provisional metrics and clinical review process before any real-world deployment claim.

## 8. Limitations

DDXPlus is synthetic and not pediatric-specific or locally representative. Filtering by age does not make it a validated pediatric Kazakhstan dataset. A successful score therefore cannot establish clinical validity, fairness across demographic groups, or safe use in practice. The protocol-derived Knowledge Base and triage rules require review by qualified clinicians and may not cover every presentation. The test set measures only the cases and labels represented in the source data; rare emergencies may have too few examples for a reliable estimate. Usability sessions with a small parent sample, if conducted, will not represent all users. The thresholds are provisional, and the architecture and test fixtures do not replace prospective clinical validation, ethics review, or regulatory assessment.
