# Recommendation Engine Architecture

**Version:** Conceptual Architecture
**Scope:** Recommendation Engine only
**Status:** Aligned with approved VersePilot architecture

This document defines the conceptual architecture of the Recommendation Engine—the implementation of VersePilot's Decision Engine. It intentionally avoids backend, database, frontend, and algorithmic design, and instead defines the responsibilities, information flow, and decision model that integrate with the approved project architecture.

---

# 1. Recommendation Engine Responsibilities

The Recommendation Engine is responsible for transforming a player's current situation into a small set of personalized, explainable Session Plans.

It does **not** own game data, persistence, external integrations, or presentation logic. Those concerns remain with the approved Backend, Data Provider Layer, Database, and Frontend architectures.

Its responsibilities are limited to answering one question:

> **"What should the player do next?"**

Specifically, the engine is responsible for:

* Understanding the player's current situation.
* Identifying viable gameplay opportunities.
* Evaluating opportunities against the player's goals and constraints.
* Building coherent gameplay strategies rather than isolated recommendations.
* Producing complete Session Plans.
* Ranking multiple strategies.
* Explaining every recommendation in human-readable language.
* Indicating the confidence of each recommendation.

The engine is **advisory**, not authoritative.

The player always makes the final decision.

---

## Architectural Boundaries

### Inputs

* Normalized player state
* Normalized game data
* Recommendation request
* External provider information

### Outputs

* Session Plans
* Strategy Briefs
* Supporting explanations
* Confidence indicators

### Explicitly Out of Scope

The Recommendation Engine does not:

* Store player data
* Synchronize external providers
* Calculate market prices
* Render user interfaces
* Execute gameplay
* Replace player choice

It consumes information and produces recommendations.

---

# 2. Input Model

The Recommendation Engine should receive two conceptual inputs:

* **PlayerSituation**
* **RecommendationRequest**

These separate the player's current state from the player's current intent.

---

## PlayerSituation

Represents the current state of the player at recommendation time.

Typical information includes:

### Player State

* Current location
* Available credits
* Available playtime
* Current ship
* Available ships
* Active equipment
* Current reputation
* Current contracts
* Current cargo
* Current vehicle availability

### Operational Context

* Solo or group session
* Current destination (if any)
* Existing travel commitments
* Existing mission chain
* Current gameplay loop

### Capability Context

Rather than asking *"What ship do they own?"*, the engine should reason about capabilities such as:

* Combat capability
* Cargo capacity
* Salvage capability
* Mining capability
* Medical capability
* Transport capability

This allows recommendations to remain independent from individual ships and simplifies future expansion.

---

## RecommendationRequest

Represents what the player wants from this session.

Examples include:

* Earn money
* Increase reputation
* Unlock mission tiers
* Purchase a ship
* Relaxed gameplay
* Short session
* Cooperative play
* Try something new
* General progression

It may also include session constraints:

* Maximum available time
* Avoid high-risk activities
* Prefer current location
* Avoid long travel
* Preferred gameplay styles

Separating intent from state allows the same player situation to produce different recommendations depending on the player's goals.

---

# 3. Output Model

The engine produces three related concepts:

* Strategy
* StrategyBrief
* SessionPlan

Each serves a different purpose.

---

## Strategy

A Strategy represents a conceptual approach to achieving the player's objective.

Examples:

* Reputation-first progression
* High-profit hauling
* Local combat contracts
* Ship purchase preparation
* Efficient bounty progression
* Low-risk income generation

A Strategy defines *how* progress will be made.

It does not yet describe the detailed gameplay sequence.

---

## StrategyBrief

A StrategyBrief is a concise summary of a Strategy.

Its purpose is comparison.

It allows players to understand why multiple strategies exist before exploring the details.

A StrategyBrief should communicate:

* Primary objective
* Expected outcome
* Estimated session length
* Expected progression
* Overall play style
* Major advantages
* Key trade-offs
* Confidence level

Multiple StrategyBriefs should normally be presented.

This reinforces that VersePilot recommends rather than dictates.

---

## SessionPlan

A SessionPlan is the detailed implementation of a Strategy.

It represents a complete gameplay session.

Typical contents include:

* Overall objective
* Recommended ship
* Recommended equipment
* Ordered activity sequence
* Estimated duration
* Expected profit
* Expected reputation gain
* Operational considerations
* Risks
* Explanation
* Confidence

A SessionPlan should optimize the entire session rather than individual activities.

---

## Relationships

```text
PlayerSituation
        +
RecommendationRequest
          │
          ▼
 Recommendation Engine
          │
          ▼
   Candidate Strategies
          │
          ▼
   Ranked StrategyBriefs
          │
          ▼
 Detailed Session Plans
```

---

# 4. Recommendation Pipeline

The Recommendation Pipeline converts player context into ranked Session Plans through a sequence of distinct conceptual stages.

Each stage has a single responsibility.

---

## Stage 1 — Candidate Generation

Purpose:

Identify all plausible approaches for the current session.

This stage focuses on possibility rather than quality.

It should discover viable gameplay opportunities based on:

* Player capabilities
* Available activities
* Current location
* Session constraints
* Goal alignment

No ranking occurs here.

---

## Stage 2 — Scoring

Purpose:

Evaluate each candidate from multiple perspectives.

Each candidate is assessed against qualitative dimensions such as:

* Progress
* Efficiency
* Suitability
* Continuity
* Risk
* Goal alignment

Scoring should remain modular so additional evaluation dimensions can be introduced without redesigning the pipeline.

---

## Stage 3 — Ranking

Purpose:

Compare candidates and determine which provide the strongest overall value for the requested session.

Ranking balances multiple considerations rather than optimizing a single metric.

The highest-ranked option becomes the primary recommendation.

Alternative high-quality strategies should also be preserved.

---

## Stage 4 — Strategy Building

Purpose:

Transform highly ranked opportunities into coherent gameplay strategies.

Rather than recommending disconnected activities, this stage organizes recommendations into meaningful approaches.

Examples:

* Reputation progression strategy
* Local earning strategy
* Ship acquisition strategy
* Mixed progression strategy

This is where the engine shifts from evaluating activities to planning gameplay.

---

## Stage 5 — Session Plan Assembly

Purpose:

Convert each strategy into a complete Session Plan.

This stage produces:

* Activity sequence
* Session flow
* Expected outcomes
* Operational guidance
* Supporting explanations
* Confidence assessment

The Session Plan is the primary product delivered to the frontend.

---

# 5. Scoring Philosophy

The Recommendation Engine should evaluate recommendations holistically.

No single factor should dominate every decision.

The objective is meaningful gameplay progression rather than maximum credits per hour.

Important evaluation dimensions include:

### Goal Alignment

How effectively the recommendation advances the player's stated objective.

---

### Profit

Expected economic progress.

Profit should be considered alongside—not above—other dimensions.

---

### Reputation Progression

Contribution toward unlocking future gameplay opportunities.

---

### Travel Efficiency

The operational cost of reaching the activity.

Recommendations requiring excessive travel should justify that investment.

---

### Operational Efficiency

The efficiency of the entire gameplay loop.

Examples include:

* Minimal downtime
* Efficient logistics
* Reduced unnecessary travel
* Limited equipment changes
* Effective use of the current location
* Productive session flow

Operational efficiency reflects how smoothly the session unfolds.

---

### Session Continuity

The engine should avoid treating every recommendation as an isolated decision.

Considerations include:

* Existing mission chains
* Current location
* Current gameplay loop
* Previously recommended activities
* Natural progression

Maintaining momentum is often more valuable than restarting with a theoretically optimal activity.

---

### Risk

Expected operational risk.

Risk is not inherently negative.

Higher-risk strategies may be appropriate when aligned with player goals.

---

### Ship Suitability

Recommendations should consider how well available ships support the proposed strategy.

This is broader than ownership and includes operational fitness.

---

### Available Playtime

Recommendations must fit within the player's available session.

Session planning should respect real-world time constraints.

---

### Player Preferences

Where known, recommendations should reflect preferred gameplay styles without becoming overly restrictive.

Preferences should guide recommendations rather than eliminate alternatives.

---

# 6. Explainability

Explainability is a first-class architectural concern.

Every recommendation should answer two questions:

* Why this strategy?
* Why not the alternatives?

Recommendations should never appear arbitrary.

Explanations should combine multiple contributing factors into clear, player-focused reasoning.

Examples include:

* Supports your goal of unlocking higher-tier contracts.
* Fits comfortably within your available session.
* Makes efficient use of your current location.
* Avoids unnecessary travel.
* Your current ship is well suited to this activity.
* Continues your existing progression path.
* Provides balanced income and reputation gains.

Where appropriate, recommendations should also acknowledge trade-offs, such as:

* Lower profit in exchange for faster reputation progression.
* Slightly longer travel for significantly greater long-term value.

Explainability should be generated alongside recommendations, not added afterward.

---

# 7. Confidence

Confidence communicates how strongly the engine believes a recommendation fits the current situation.

It should represent recommendation quality rather than certainty of game outcomes.

Confidence may reflect factors such as:

* Completeness of available information.
* Alignment with player goals.
* Stability of supporting data.
* Availability of suitable alternatives.
* Consistency across evaluation dimensions.

Confidence is intended to help players interpret recommendations, not replace their judgment.

Low confidence does not imply a poor recommendation; it indicates that multiple options may be similarly appropriate or that available information is limited.

---

# 8. Future AI Integration

AI should enhance the Recommendation Engine without replacing its deterministic decision-making.

The core recommendation pipeline should remain transparent, testable, and explainable.

Appropriate future AI enhancements include:

* Producing more natural Strategy Briefs.
* Personalizing explanation wording.
* Summarizing trade-offs between alternative Session Plans.
* Identifying long-term gameplay patterns.
* Learning presentation preferences.
* Suggesting future player goals based on historical behavior.

AI should **not** determine the underlying recommendation logic, ranking, or strategy selection.

This preserves reproducibility, explainability, and trust while allowing AI to improve communication and personalization.

---

# 9. MVP Scope

## Included in MVP

* PlayerSituation as defined by the approved player model (location, ship, credits, available playtime, and goal, with optional use of normalized provider data as available).
* RecommendationRequest capturing the player's immediate objective and session constraints.
* Candidate Generation for viable activities.
* Modular qualitative scoring dimensions.
* Strategy construction.
* Up to three ranked Strategy Briefs.
* Detailed Session Plan generation.
* Human-readable explanations for every recommendation.
* Conceptual confidence indicators.
* Session optimization rather than single-activity optimization.
* Deterministic recommendation pipeline integrated with the approved backend architecture.

## Future Enhancements

* Multi-session progression planning.
* Adaptive preference learning.
* Historical session continuity across multiple play sessions.
* Fleet-wide optimization.
* Cooperative group planning.
* Dynamic event awareness.
* Economic trend awareness.
* AI-enhanced narrative explanations.
* Long-term progression forecasting.
* Personalized recommendation profiles.
* Scenario simulation ("What if I rent a Vulture first?").

## Complexity Guidance

To remain faithful to VersePilot's mission and MVP philosophy, the Recommendation Engine should resist premature sophistication. The initial implementation should focus on producing a small number of high-quality, explainable Session Plans from reliable inputs rather than attempting exhaustive optimization or predictive modeling. Every additional capability should be justified by a measurable improvement in answering the core question:

> **"What should the player do next?"**
