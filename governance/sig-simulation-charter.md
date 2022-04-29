# SIG-Simulation Charter

This charter adheres to the Roles and Organization Management specified by the [O3DE Foundation](https://github.com/o3de/community). Team information may be found in the [README](../README.md).

## Overview of SIG

Sig-simulation oversees aspects of O3DE related to modelling real-world processes which evolve over time. Its responsibilities include:

- Ensuring efficient runtime performance and stability of all simulation operations
- Keeping pace with the latest developments in simulation technology

## Goals

- Review and contribute to simulation technology roadmap
- Review simulation related RFCs and PRs
- Ensure timely and frequent communication of simulation activities (including Discord, sig meetings, triage, PR review, RFCs, etc.)
- Ensure accurate measurement of simulation systems (testing and performance)
- Provide support to community for simulation topics
- Work with sig-core to unify, consolidate and maintain math libraries
- Work with sig-core to define tick (update) system
- Operational excellence on simulation technology (unit tests, end-to-end tests, benchmarks, documentation, tutorials)

## Scope

- Physics API (AzFramework/Physics), physics related Gems (e.g. PhysX Gem) and physics tools
- Animation systems, EMotionFX math library, related Gems (e.g. EMotionFX Gem) and animation tools
- Character physics (e.g. character controllers, gravity, ragdolls)
- Cloth simulation (e.g. NvCloth Gem)
- Destruction (e.g. Nvidia Blast Gem)
- Debugging tools for simulations
- Exposing simulation features to scripting (Script Canvas, Lua, Python)
- Editor "Simulate" mode (Ctrl+P)
- Shape components (in co-operation with sig-content)
- AI systems (e.g. pathfinding, behavior trees)
- Potential future simulation features (e.g. fluids, particles)

## Cross-cutting Processes

- Collaborate with sig-core on:
  - Math library maintenance
  - Tick (update) system maintenance
- Collaborate with sig-graphics-audio on:
  - Debug rendering
  - Presentation aspects of simulations
  - Skinned mesh system
- Support sig-content work on:
  - Simulation aspects of terrain system
- Collaborate with sig-network on:
  - Multiplayer simulation

## Out of Scope

- The presentation aspects of simulated systems (e.g. rendering)
- Visibility and culling
- Scripting infrastructure (e.g. Behavior Context, Script Canvas framework)

## SIG Links and Lists

- Joining this SIG
- [Joining Slack/Discord](https://discord.gg/6nSYcUBaR3)
- Mailing list
- Issues/PRs
- Meeting agenda & notes

## Roles and Organization Management

SIG Simulation adheres to the standards for roles and organization management as specified by the [O3DE Foundation](https://github.com/o3de/community). This SIG opts in to updates and modifications to the Foundation's guidance.
