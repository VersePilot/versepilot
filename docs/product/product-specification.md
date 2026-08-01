# Product Specification

## Overview

VersePilot is an open-source decision engine for Star Citizen.

VersePilot does not aim to replace existing community tools or become another Star Citizen database. Instead, it integrates with existing data providers to help players answer a single question:

> **What should I do next?**

The Recommendation Engine analyzes the player's current situation and generates actionable Session Plans tailored to their available ships, reputation, location, available playtime, goals, and preferred play style.

---

# Vision

VersePilot should reduce decision fatigue and help players spend more time playing and less time planning.

Rather than presenting raw game data, VersePilot provides clear, explainable recommendations supported by existing community resources.

---

# Core Principles

- VersePilot is a decision engine.
- VersePilot is not a Star Citizen database.
- Prefer integration over duplication.
- Every recommendation should be explainable.
- Recommendations should consider the player's available time.
- Players should understand *why* a recommendation was made.

---

# MVP Goals

The MVP focuses on one primary workflow:

Current Situation

↓

Generate Session Plan

↓

Review Strategy Briefs

↓

Play

The MVP should prove that VersePilot can generate useful recommendations using a small set of mock data before integrating live providers.

---

# Long-Term Vision

Future versions of VersePilot may include:

- Live provider integrations
- Personalized recommendations
- Crew recommendations
- Cooperative planning
- Crew discovery (LFG)
- Recommendation history
- Advanced gameplay loop planning

---

# Guiding Question

Every feature should help answer:

> **What should the player do next?**
