I agree with all five refinements. They simplify the MVP while reinforcing the core philosophy that VersePilot is a **decision engine**, not a traditional multi-page application. Here's the architecture with those changes incorporated.

---

# Frozen Frontend Architecture (MVP)

## Frontend Philosophy

Replace the original statement with:

> **The frontend displays recommendations. It never creates them.**

All recommendation logic, scoring, prioritization, and strategy generation belong exclusively to the backend. The frontend is responsible for:

* Collecting the player's current situation.
* Sending that context to the backend.
* Presenting the returned Session Plan clearly.
* Supporting progressive disclosure.
* Helping the player understand *why* a strategy was recommended.

---

# User Flow

The MVP flow becomes:

```text
Current Situation

↓

Generate Session Plan

↓

Session Plan

↓

Expand Strategy Brief

↓

Play
```

There is no separate Strategy Brief page. Expanding a strategy keeps the player within the Session Plan, preserving context and making comparison effortless.

---

# Routing Strategy

The routing is intentionally minimal:

```text
/

Current Situation

↓

/plan

Session Plan
```

Only two routes are required for the MVP. This keeps navigation shallow and focused on the primary workflow.

---

# Current Situation

The input form should contain only the information needed to generate useful recommendations:

* Ship selection
* Current location
* Goal
* Available playtime

**Credits are intentionally omitted** from the MVP to reduce friction. They can be introduced later when recommendations begin considering purchases or financial planning.

---

# Session Plan

The Session Plan page displays multiple strategy options returned by the backend.

Each strategy is represented by a **StrategyBriefCard**.

Displayed information includes:

* Strategy title
* Estimated duration
* Expected rewards
* Reputation impact
* Recommended ship
* Short rationale

Each card includes an expand action.

---

# Progressive Disclosure

Expanding a **StrategyBriefCard** reveals additional detail in place.

The expanded content includes:

* Mission sequence
* Equipment or preparation
* Risks
* Expected outcomes
* Detailed explanation of why the backend selected this strategy

This approach preserves comparison between strategies without forcing navigation.

---

# Updated Component Hierarchy

```text
App

ApplicationLayout

    Header

    Router

        CurrentSituationPage

            SituationForm

                ShipSelector

                GoalSelector

                LocationSelector

                PlaytimeInput

                GenerateButton

        SessionPlanPage

            SessionPlanHeader

            StrategyBriefList

                StrategyBriefCard

                StrategyBriefCard

                StrategyBriefCard
```

Each **StrategyBriefCard** manages its own expanded state, keeping the interaction localized and straightforward.

---

# Shared Component Library

The shared component library remains presentation-focused:

* Button
* Card
* Badge
* Tag
* Input
* NumberInput
* Select
* RadioGroup
* Checkbox
* Accordion
* Divider
* Modal
* Drawer
* Tooltip
* Spinner
* Skeleton
* Alert
* Toast
* EmptyState

No recommendation-specific behavior belongs in shared components.

---

# State Management

The state strategy remains intentionally lightweight.

**Local UI state**

* Form values
* Expanded strategy cards
* Dialogs
* UI interactions

**Server state**

Managed by TanStack Query for:

* Session Plan generation
* Caching
* Refetching
* Loading states
* Error handling

No global client-side store is required for the MVP.

---

# API Integration

The interaction model remains clean:

```text
Player Input

↓

POST /session-plan

↓

Decision Engine

↓

Session Plan

↓

Render StrategyBriefCards
```

The frontend does not transform backend recommendations into new recommendations. It simply renders the response using UI-friendly view models where needed.

---

# Styling Strategy

Tailwind CSS remains the styling solution, with a small set of design tokens for:

* Spacing
* Typography
* Border radius
* Colors
* Shadows

The visual design should emphasize:

* Calm layouts
* Generous whitespace
* Consistent card structure
* Minimal visual noise
* A navigation-inspired aesthetic

---

# Error Handling

Every asynchronous interaction should support four clear states:

* Loading
* Success
* Empty
* Error

Errors should present concise, actionable messaging with retry options. Technical details remain in logs, not in the UI.

---

# Loading States

Prefer skeleton placeholders for content already expected by the user, such as Session Plans and Strategy Briefs.

Reserve full-page spinners for application initialization or route-level transitions.

---

# Future Scalability

The feature-based architecture remains suitable for future additions such as Fleet, Progress, Planner, or Account management without restructuring the application.

The frontend continues to scale by adding isolated feature modules while keeping recommendation logic centralized in the backend.

---

## Final Architecture Decision

These refinements produce a frontend that is even more aligned with VersePilot's core principles:

* **Minimal navigation** keeps players oriented.
* **Inline expansion** enables direct comparison of strategies.
* **Reduced input friction** improves the primary workflow.
* **Consistent terminology** matches the backend and product specification.
* **Strict separation of concerns** ensures the frontend never generates recommendations.

This architecture is lean, maintainable, and focused on the MVP's single objective: **generate and present Session Plans with clear, expandable Strategy Briefs**.
