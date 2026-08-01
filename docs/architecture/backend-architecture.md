Approved. I think all three refinements improve the architecture without adding unnecessary complexity. In particular, they make the ubiquitous language of the project much clearer.

## 1. RecommendationEngine

I agree with the rename.

The distinction becomes:

* **VersePilot** = the product (the decision engine)
* **RecommendationEngine** = the backend component that computes recommendations

That avoids the common confusion of having a class named `DecisionEngine` inside a product already called a decision engine.

The core interface becomes:

```python
class RecommendationEngine:
    async def generate(
        self,
        request: RecommendationRequest,
    ) -> SessionPlan:
        ...
```

Internally, the implementation can evolve freely without affecting the API.

---

# 2. Recommendation Pipeline

I strongly prefer thinking of this as a pipeline of independent responsibilities rather than one large algorithm.

```text
RecommendationRequest
        │
        ▼
CandidateGenerator
        │
        ▼
ScoringEngine
        │
        ▼
RankingEngine
        │
        ▼
StrategyBuilder
        │
        ▼
SessionPlanBuilder
        │
        ▼
SessionPlan
```

Each stage has a single responsibility:

### Candidate Generator

Produces every feasible action for the player's current situation.

Examples:

* Cargo hauling
* Mercenary contract
* Mining run
* Salvage operation
* Reputation grind

No scores.

No sorting.

Just valid candidates.

---

### Scoring Engine

Evaluates each candidate independently.

Possible future inputs include:

* Profit
* Reputation gain
* Travel time
* Risk
* Ship suitability
* Player goals
* Available playtime

Output:

```text
Candidate
↓

ScoredCandidate
```

---

### Ranking Engine

Responsible only for ordering recommendations.

It knows nothing about presentation.

Input:

```text
ScoredCandidate[]
```

Output:

```text
RankedCandidate[]
```

---

### Strategy Builder

Transforms highly ranked candidates into domain-level `Strategy` objects.

For example:

```text
Top Candidate

↓

Strategy
```

A strategy can aggregate multiple actions into a coherent gameplay approach rather than exposing raw candidates.

---

### SessionPlanBuilder

Presentation layer of the domain.

Groups strategies into the final session plan returned to the client.

It is responsible for organization, not decision making.

---

# 3. Strategy as a Core Domain Object

I think this is the most important refinement.

Instead of:

```text
SessionPlan
    ├── Strategy
```

the dependency becomes:

```text
PlayerSituation
        │
RecommendationRequest
        │
        ▼
Strategy
        │
        ▼
StrategyBrief
        │
        ▼
SessionPlan
```

This better reflects the business model.

The Recommendation Engine produces **strategies**.

The Session Plan is simply the delivery format.

That makes `Strategy` a first-class domain concept alongside `PlayerSituation` and `SessionPlan`.

---

# Revised Domain Model

I would now consider these the core domain objects:

```text
PlayerProfile
PlayerSituation

RecommendationRequest

Strategy

StrategyBrief

SessionPlan
```

Supporting value objects can include:

```text
Goal

AvailablePlayTime

Location

Ship

Reputation

Credits
```

These provide context for recommendations without becoming persistence concerns.

---

# Revised Project Structure

With these refinements, the domain becomes more expressive while remaining compact:

```text
app/
├── api/
│   ├── routes/
│   └── schemas/
│
├── application/
│   └── services/
│       └── session_plan_service.py
│
├── domain/
│   ├── models/
│   │   ├── player_situation.py
│   │   ├── recommendation_request.py
│   │   ├── strategy.py
│   │   ├── strategy_brief.py
│   │   └── session_plan.py
│   │
│   └── interfaces/
│       └── recommendation_engine.py
│
├── infrastructure/
│   ├── decision/
│   │   ├── recommendation_engine.py
│   │   ├── candidate_generator.py
│   │   ├── scoring_engine.py
│   │   ├── ranking_engine.py
│   │   ├── strategy_builder.py
│   │   ├── session_plan_builder.py
│   │   └── mock_recommendation_engine.py
│   │
│   ├── database/
│   ├── providers/
│   └── repositories/
│
└── main.py
```

One small refinement I'd make is to name the infrastructure package `recommendation/` instead of `decision/`, to stay consistent with the new terminology:

```text
infrastructure/
    recommendation/
        recommendation_engine.py
        candidate_generator.py
        scoring_engine.py
        ranking_engine.py
        strategy_builder.py
        session_plan_builder.py
```

This keeps the naming aligned throughout the codebase.

## Architectural Principle

With these refinements, the backend has a clear and stable responsibility:

```text
PlayerSituation
        │
RecommendationRequest
        │
RecommendationEngine
        │
Strategy[]
        │
StrategyBrief[]
        │
SessionPlan
```

The HTTP API, application layer, and persistence remain unchanged as the recommendation logic evolves. Future improvements—whether to candidate generation, scoring algorithms, or ranking heuristics—can be made within the `RecommendationEngine` pipeline without breaking the external API or the surrounding architecture. That separation gives VersePilot a solid foundation while staying true to the MVP goal of building a clean, extensible backend before implementing intelligence.
