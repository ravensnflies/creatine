# CREATINE!

**Fitness Progression, Body Mass Milestones & Special Abilities for Project Zomboid Build 42**

Developed by **Raven**

## Overview

CREATINE! is a character-progression mod that combines nutrition, body weight, exercise regularity, Fitness, and Strength into a unified progression system.

The mod allows players to develop increasingly powerful survivors through consistent supplementation, physical training, and weight management.

Rather than awarding abilities through a simple experience counter, CREATINE! evaluates multiple aspects of the character's current state.

The system is designed to reward sustained progression while maintaining meaningful requirements for earned abilities.

## Features

### Seven-Day Creatine Loading

Players must consume an eligible creatine product on seven distinct in-game days to complete the loading phase.

The progression system records qualifying days and prevents multiple doses on the same world day from providing additional loading credit.

Supported consumption includes:

- Creatine powder
- Creatine smoothies
- Supported supplement shakes containing the required creatine ingredient

### Fitness and Weight Milestones

Once fully loaded, players can unlock four cumulative progression tiers.

| Milestone | Weight | Fitness | Strength |
|---|---:|---:|---:|
| Bully | 85 kg | 5 | 5 |
| Tactician | 95 kg | 6 | 7 |
| Berserker | 110 kg | 8 | 9 |
| Unyielding Will | 130 kg | 10 | 10 |

New milestone awards additionally require at least three distinct exercise types with 25% or greater regularity each.

Milestones grant associated trait packages:

**Bully**
- Nutritionist
- Thick Skinned
- Resilient

**Tactician**
- Graceful
- Gymnast
- Hunter

**Berserker**
- Adrenaline Junkie
- Athletic
- Axeman

**Unyielding Will**
- Rage
- Bulldoze

Milestone qualification is evaluated against current character attributes. Losing required weight or skill levels can revoke previously earned milestone benefits.

The implementation tracks traits granted by the mod to avoid removing traits the character possessed independently.

### Rage

Rage is an emergency survival ability associated with Unyielding Will.

Its implementation monitors incoming damage and character health, activating when health reaches or would fall to a critical threshold.

The ability can:

- Interrupt qualifying incoming damage
- Restore the survivor's health and injuries
- Provide brief damage protection
- Eliminate nearby zombies
- Enter a cooldown period

The current uploaded Lua implementation uses a seven-in-game-day cooldown.

### Bulldoze

Bulldoze gives a sufficiently developed survivor the ability to eliminate zombies directly in their running path.

The implementation evaluates:

- Whether the character is running
- Nearby zombie positions
- Relative facing direction
- Distance from the player
- Whether the required trait is active

Targets are restricted by a forward-facing cone and proximity threshold.

The feature includes separate execution paths for single-player and dedicated-server environments.

### Creatine Smoothies and Supplement Compatibility

CREATINE! provides consumable items and smoothie recipes to support the nutrition and body-mass progression system.

Optional compatibility includes integration with Junkiez More Workout Supplements.

The compatibility logic inspects ingredients so that an ordinary supplement shake does not automatically count as a creatine dose.

## Technical Architecture

CREATINE! separates progression, item consumption, trait registration, and special abilities into distinct Lua components.

| Component | Responsibility |
|---|---|
| `CREATINE_Master.lua` | Progression rules, milestone evaluation, dosing, and persistence |
| `CREATINE_OnEat.lua` | Consumption callbacks and supplement integration |
| `CREATINE_Distribution.lua` | Item distribution integration |
| `CREATINE_TraitInit.lua` | Custom trait registration |
| `CREATINE_RageTrait.lua` | Rage activation and cooldown behavior |
| `CREATINE_BulldozeTrait.lua` | Running-state and zombie interaction logic |

### Stateful Progression

The mod maintains persistent progression information including:

- Recorded dosing days
- Loading status
- Active milestones
- Traits granted by the mod
- Relevant character progression data

The implementation uses player ModData and, in multiplayer, a world-level Global ModData ledger.

A dedicated-server persistence issue prompted investigation into the differences between character-local data and world-saved data.

The multiplayer ledger uses player usernames as account identifiers. As a result, persisted progression is account-scoped rather than automatically character-specific.

### Event-Driven Integration

CREATINE! interacts with Project Zomboid through existing game events and APIs.

Relevant integration points include:

- Item consumption callbacks
- Client/server command handling
- Periodic progression evaluation
- Fitness and exercise regularity APIs
- Character trait registration
- Damage and player-update events
- Server tick processing
- ModData persistence

These integrations allow the mod to operate within the game's existing character and simulation systems.

### Multiplayer Troubleshooting

Dedicated-server testing exposed differences between local-player and remote-player update behavior.

In particular, the Bulldoze implementation accounts for remote players not following the same update path as locally controlled characters.

The current approach uses server tick processing for dedicated multiplayer and player-update events for single-player execution.

This was an integration and execution-context problem, not merely a syntax problem.

### Diagnostics and Defensive Logic

The project includes diagnostic output and state checks for:

- Dose-history reconciliation
- Missing or inconsistent saved data
- Duplicate same-day doses
- Exercise qualification
- Milestone activation
- Trait registration and behavior
- Multiplayer execution differences
- Optional dependency detection

## Known Limitations and Areas for Improvement

The current source includes several areas that warrant further verification.

**Consumption authority:** The server validates the submitted dose item type, but a client notification alone is not authoritative proof that the item was consumed. Additional server-verifiable consumption checks would strengthen resistance to modified-client abuse.

**Persistence:** Dose-history reconciliation includes diagnostic safeguards, but unusual save, restart, and migration scenarios require continued testing.

**Multiplayer abilities:** Special abilities involving damage and entity state should be regression-tested across supported dedicated-server builds.

**Compatibility:** Game updates and third-party changes to consumption callbacks, fitness systems, and trait behavior can affect functionality.

The presence of diagnostic handling does not imply that every persistence or synchronization issue has been conclusively resolved.

## Development Methodology

**CREATINE! was developed using an AI-assisted implementation and iterative testing workflow directed by Raven.**

My responsibilities included:

1. Designing the fitness and milestone progression model.
2. Defining the relationship between body mass, exercise, and character abilities.
3. Researching game APIs, existing events, and integration opportunities.
4. Directing AI-generated implementations around those requirements.
5. Testing gameplay behavior and multiplayer execution.
6. Reviewing diagnostic logs to isolate incorrect behavior.
7. Identifying persistence and synchronization problems.
8. Revising implementation requirements based on testing results.

The project reflects experience in systems design, behavioral evaluation, API research, debugging, and integration testing.

I do not claim independent proficiency in writing the complete Lua implementation without AI assistance.

My focus is understanding what a system must accomplish, how it interacts with its environment, and whether its implementation produces the intended results.

## Requirements

- Project Zomboid Build 42.20 or later within the compatible Build 42 series
- No mandatory third-party supplement mod
- Optional compatibility with supported supplemental workout mods

The uploaded implementation includes Build 42.21-specific adjustments. Compatibility with future builds is not guaranteed.

## Installation

Install CREATINE! through its supported Project Zomboid mod or Workshop distribution.

Enable it in the game or multiplayer server configuration.

Optional supplement integrations require their respective mods.

## Credits

**Project direction, gameplay systems design, API investigation, testing, and AI-assisted development:** Raven

**Game:** Project Zomboid by The Indie Stone

**Compatibility ecosystem:** Respective third-party mod authors

---

*CREATINE! is an independently developed Project Zomboid mod and is not affiliated with or endorsed by The Indie Stone.*
