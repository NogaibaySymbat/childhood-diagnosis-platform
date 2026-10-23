# Risks and Project Management

**Document:** 09-risks-and-management.md
**Project:** Development of an Intelligent Platform for Preliminary Diagnosis of Childhood Diseases
**Author:** Nogaibay Symbat
**Last updated:** 2026-10-23
**Status:** Draft for the first probationary submission

---

## 1. Team and responsibilities

| Member | Group | Area of responsibility | Deliverables owned |
|---|---|---|---|
| Sabyr Aruzhan | SE-2402 | Requirements, user research, frontend and UX | Project proposal, requirements specification, relevance evidence, UI prototype, README |
| Nogaibay Symbat | SE-2402 | Backend, repository and development process, domain analysis | Repository structure, literature review, existing systems comparison, risk and management plan |
| Sagynbaeva Zhanat | SE-2402 | Model, architecture, technology decisions | Technology comparison, architecture and diagrams, verification plan |

**Supervisor:** Yermaganbetova Madina Askarovna

Shared responsibilities: code review of every pull request, supervisor meetings, the submission package.

## 2. How the work is run

**Repository.** `main` is the reviewed baseline and is protected: direct pushes are blocked, and merging requires one approval from another team member. All work goes through task branches named `DIP-<id>-<short-name>` and pull requests that reference the YouTrack task.

**Task tracking.** YouTrack project with the key `DIP`, matching the commit prefixes. Every task carries an assignee, a due date and acceptance criteria. A task moves to Done only when its acceptance criteria are met and the corresponding pull request is merged.

**Review.** Every change is reviewed by a team member who did not author it. Review comments and the revisions they produced remain visible in the pull request history.

**Supervisor involvement.** Deliverables are reviewed by the supervisor before each submission; comments and the resulting changes are recorded in `docs/10-supervisor-feedback.md` with links to the commits that implemented them.

## 3. Milestones

| Milestone | Date | Gate |
|---|---|---|
| Repository and tracker set up | 22 October 2026 | Protected `main`, YouTrack project with tasks assigned |
| Analysis and design complete | 24 October 2026 | All eleven sections present; version tagged `prob-period-1` |
| Knowledge base built | December 2026 | Target conditions encoded from MoH RK protocols and reviewed by the supervisor |
| Triage layer implemented | January 2027 | Every red-flag rule covered by a test |
| Model trained and evaluated | February 2027 | Metrics measured against the baseline per `docs/08-verification-plan.md` |
| Application implemented | March 2027 | Parent application and pediatrician dashboard pass functional verification |
| Final evaluation and report | April 2027 | Acceptance thresholds met or limitations documented |

## 4. Risk register

Likelihood and impact are rated low, medium or high. Risks are ordered by severity.

| ID | Risk | Likelihood | Impact | Mitigation | Owner |
|---|---|---|---|---|---|
| R-1 | An urgent case is classified as non-urgent | Medium | Critical — potential harm to a child | Triage produced by deterministic rules independent of the model; red flags always displayed; urgent-case recall is the primary metric and takes precedence over ranking accuracy | Zhanat |
| R-2 | No real paediatric symptom dataset for the local context | High — already realised | High | Knowledge base built manually from MoH RK protocols; open datasets used only for architecture validation; limitation stated openly | Zhanat |
| R-3 | Users treat the output as a diagnosis and self-medicate | Medium | High | Persistent disclaimer; wording centred on urgency of seeing a doctor rather than naming a disease; no medication or dosage advice anywhere in the product | Aruzhan |
| R-4 | Too few pediatrician interviews to ground the requirements | High | Medium | Interviews started early and conducted asynchronously; parent interviews as a supplement; unconfirmed findings recorded as assumptions rather than evidence | Aruzhan |
| R-5 | Knowledge base coverage too narrow to be useful | Medium | High | Conditions prioritised by frequency in primary care; out-of-scope symptom sets return an explicit "cannot assess" rather than a guess | Symbat |
| R-6 | Personal health data of children exposed | Low | Critical | Encryption at rest, HTTPS in transit, role-based access, cascading deletion, no credentials in the repository, `.gitignore` covering environment files | Symbat |
| R-7 | Model fails to beat the rule-based baseline | Medium | Medium | Baseline is a working system in its own right and ships if it wins; the outcome is reported as a finding rather than a failure | Zhanat |
| R-8 | Uneven contribution across the team | Medium | Medium | Per-person tasks in YouTrack with deadlines; contribution visible in commit history and pull requests; imbalance raised with the supervisor early rather than at submission | All |
| R-9 | Deadlines missed due to parallel coursework | High | Medium | Work split so that each deliverable has a single owner; drafts submitted rather than nothing; milestones reviewed weekly | All |
| R-10 | Deployment environment not available | Medium | Low | Application runs locally for the defense; deployment treated as optional scope | Symbat |
| R-11 | Medical content incorrect or outdated | Medium | High | Every clinical rule traced to a protocol; supervisor reviews medical content; protocol version recorded | Symbat |

## 5. Dependencies

| Dependency | Blocks | Status |
|---|---|---|
| Knowledge base built from protocols | Triage layer, model training, evaluation | Not started |
| Supervisor confirmation of target metrics | Acceptance thresholds in the verification plan | Pending |
| Deployment environment confirmed | Deployment, performance measurement conditions | Pending |
| Pediatrician interviews | Converting assumptions in the requirements into verified findings | In progress |
| Literature review conclusions | Final list of conditions for the knowledge base | In progress |

## 6. Decision log

| Date | Decision | Rationale |
|---|---|---|
| 21 Oct 2026 | Repository moved from GitLab to GitHub | Team preference; single repository for the submission |
| 22 Oct 2026 | `main` protected, one approval required | Requirement that changes are reviewed before reaching the baseline |
| 22 Oct 2026 | YouTrack project key set to `DIP` | Match the commit prefixes already in the history so tasks trace to changes |
| 22 Oct 2026 | Ranking and triage separated into distinct components | Urgency must be reproducible and must not depend on a probabilistic model |
| 22 Oct 2026 | Hybrid approach chosen over an end-to-end model | Explanation required by FR-11 must come from the knowledge base; no local training corpus exists |

## 7. Communication

Day-to-day coordination in the team chat; work items in YouTrack; technical discussion in pull request comments so that it stays attached to the change. Supervisor meetings before each submission, with outcomes recorded in `docs/10-supervisor-feedback.md`.
