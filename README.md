# VersePilot

> **Make every session count.**

VersePilot is an open-source decision engine for **Star Citizen**.

Rather than acting as another game database, VersePilot analyzes a player's current situation and recommends the **best thing to do next**. The result is a personalized **Session Plan** designed to help players make meaningful progress toward their goals every time they play.

---

# Why VersePilot?

Star Citizen offers incredible freedom, but that freedom often creates a simple question:

> **"What should I do next?"**

Should you:

- Haul cargo?
- Mine resources?
- Salvage wrecks?
- Trade commodities?
- Farm blueprints?
- Save for a new ship?

The answer depends on your ships, credits, location, available playtime, and personal goals.

VersePilot exists to answer that question.

**It helps players spend less time planning and more time playing.**

---

# What is a Session Plan?

A Session Plan is the primary output of VersePilot.

Instead of recommending a single mission or activity, VersePilot generates a complete plan for your current play session.

A typical Session Plan includes:

- Recommended activity
- Expected earnings or progression
- Suggested ship
- Required equipment
- Estimated session length
- Risk level
- Step-by-step actions
- An explanation of why this plan was selected

The objective is simple:

> **Spend less time deciding and more time playing.**

---

# Core Principles

VersePilot is built around a few simple principles.

- Build intelligence, not databases.
- Integrate with existing community resources.
- Keep recommendations player-centric.
- Explain every recommendation.
- Optimize long-term progression.
- Keep the MVP focused and simple.

Every feature should ultimately answer one question:

> **What should the player do next?**

---

# Not Another Database

VersePilot intentionally avoids duplicating work already done by the Star Citizen community.

Instead, it integrates with existing community resources wherever possible.

Potential integrations include:

- SCMDB
- UEX
- Strata
- Erkul
- Star Citizen Wiki
- Future official APIs

These projects provide excellent information.

VersePilot's job is to transform that information into actionable recommendations.

---

# MVP

The initial MVP focuses on proving one concept:

Generate a useful Session Plan from a small set of player inputs:

- Current ship
- Current location
- Current credits (aUEC)
- Current goal
- Available playtime

Nothing more.

---

# Technology

## Backend

- Python
- FastAPI
- SQLAlchemy
- Alembic
- SQLite (initially)
- PostgreSQL (future)

## Frontend

- React
- TypeScript
- Vite

---

# Documentation

Project documentation is available in the `docs/` directory.

Current documentation includes:

- Product Specification
- Backend Architecture
- Frontend Architecture
- Database Architecture
- Recommendation Engine Architecture

Additional documentation covering providers, gameplay loops, and architectural decisions will be added as the project evolves.

---

# Contributing

VersePilot is an open-source community project.

Contributions, feedback, ideas, and discussions are welcome as the project evolves.

Contribution guidelines will be added as the repository matures.

---

# License

This project is licensed under the MIT License.

---

## Make every session count.
