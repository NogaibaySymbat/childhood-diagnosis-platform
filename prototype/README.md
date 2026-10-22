# UI Prototype

Interactive prototype of the mobile application, covering the main user scenarios for the parent role.

## How to open

Open `kidcheck-prototype.html` in any browser. The file is self-contained — no server, no build step, no dependencies.

## Screens

| # | Screen | Scenario |
|---|---|---|
| 1 | Home | Active child profile, entry point to a check, recent checks |
| 2 | Symptom intake | Symptom search and selection, duration, child context |
| 3 | Result | Urgency verdict, ranked conditions with matching and missing symptoms, home-care steps, red-flag warnings |
| 4 | Pediatrician chat | Doctor sees the check result, asks follow-up questions, offers an appointment |
| 5 | History | Past checks grouped by month, agreement with the doctor's diagnosis |
| 6 | Child profile | Several children, vaccinations, allergies, language and privacy settings |

Navigation works through the bottom tab bar and through the actions inside each screen. Symptoms can be toggled.

## What the prototype demonstrates

- Structured symptom intake rather than free-text input (FR-05 to FR-09)
- Ranked conditions with an explanation of what supports each one (FR-10, FR-11)
- Urgency level and red-flag block shown independently of the ranking (FR-12, FR-13)
- Disclaimer visible on the result screen without scrolling (FR-14)
- Routing to a pediatrician with the check attached (FR-19)
- Three-language switch (FR-26)

## Status

Design prototype only. The data shown is illustrative and is not produced by the assessment model. Screenshots of each screen are in `screens/`.
