Approved. Those changes strengthen the separation between **persistent player state** and **session state**, and they align well with the agreed backend architecture where the Decision Engine operates on a `PlayerSituation` submitted with each Session Plan request rather than relying on stored transient state. 

## Updated MVP Data Architecture

### 1. Player Profile

The persistent player profile now contains only long-lived information owned by the player.

**Table: `players`**

* id
* name
* credits
* created_at
* updated_at

`current_location` has been removed because location is part of the current session context rather than the persistent profile.

---

### 2. Owned Ships

**Table: `owned_ships`**

* id
* player_id
* provider_ship_id
* custom_name
* favorite

Purpose:

Persist only the player's ownership information.

Ship specifications continue to come from external providers.

---

### 3. Player Reputation

**Table: `player_reputation`**

* id
* player_id
* faction
* reputation

Purpose:

Persist progression that belongs to the player.

---

### 4. Player Preferences

**Table: `player_preferences`**

* player_id
* career_preferences
* risk_profile
* session_defaults

Purpose:

Store long-lived preferences used to personalize recommendations.

---

### 5. Provider Synchronization

**Table: `provider_sync`**

* provider
* last_sync
* etag
* version

Purpose:

Track synchronization state only.

No provider business data is stored as authoritative records.

---

### 6. Application Settings

**Table: `application_settings`**

* key
* value

Purpose:

Persist application-level configuration.

---

# Removed from MVP

## `current_location`

Removed from `players`.

Reason:

Current location belongs to the `PlayerSituation` supplied with each Session Plan request, not the persistent player profile.

---

## `player_goals`

Removed entirely.

Reason:

Goals are session-specific inputs to the Decision Engine and should be submitted with each request rather than persisted.

This keeps the database focused on durable player state.

---

## `recommendation_history`

Deferred until post-MVP.

Reason:

While useful for personalization, analytics, and future learning, it is not required to generate Session Plans in the MVP.

---

# Updated MVP Schema

```text
players
--------
id
name
credits
created_at
updated_at

owned_ships
-----------
id
player_id
provider_ship_id
custom_name
favorite

player_reputation
-----------------
id
player_id
faction
reputation

player_preferences
------------------
player_id
career_preferences
risk_profile
session_defaults

provider_sync
-------------
provider
last_sync
etag
version

application_settings
--------------------
key
value
```

# Session State vs. Persistent State

This revision establishes a clear architectural boundary:

## Persistent (SQLite)

* Player profile
* Credits
* Owned ships
* Reputation
* Preferences
* Provider sync metadata
* Application settings

## Session Request (Not Persisted)

* Current location
* Available playtime
* Current goals
* Current party (solo/group)
* Available ships for this session
* Current mission context
* Risk appetite for this session
* Other transient inputs required to build a `PlayerSituation`

This separation reinforces the backend's existing `PlayerSituation → SessionPlan` workflow by ensuring that transient decision inputs remain part of the request model rather than the persistence layer. 

# Final MVP Scope

The approved MVP persistence layer consists of six tables:

1. `players`
2. `owned_ships`
3. `player_reputation`
4. `player_preferences`
5. `provider_sync`
6. `application_settings`

This provides a stable foundation for the backend while remaining true to the project's guiding principle: **VersePilot stores only player-owned, long-lived data and relies on external providers for Star Citizen game data.**
