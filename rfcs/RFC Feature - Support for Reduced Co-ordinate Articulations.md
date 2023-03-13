# Support for reduced co-ordinate articulations

## Summary

This RFC proposes exposing articulations, a feature in PhysX designed to provide much more robust simulation of complex structures of rigid bodies (links) connected by joints.

## What is the relevance of this feature?

Robotics is a domain in which O3DE shows a lot of potential. Complex structures with multiple joints have many uses cases in robotics, such as manipulator arms. It is currently possible to simulate these structures using the rigid body and joint components already provided in the existing Nvidia PhysX integration. However there are a number of issues with using joints:

* Poor stability (joint constraints can become violated or in extreme cases the simulation can explode).
* Poor handling of high mass ratios between constrained bodies.
* A lot of tuning may be required to achieve stability. This may include increasing solver iterations and reducing simulation timesteps, which can damage performance.

Exposing articulations will provide much more robust simulation for these use cases.

## Feature design description

## Technical design description

### Choice of articulation type

Nvidia PhysX provides two types of articulations (see [Nvidia documentation](https://docs.omniverse.nvidia.com/prod_extensions/prod_extensions/ext_physics/articulations.html)):

* **Maximal co-ordinate articulations** These use more co-ordinates than there are degrees of freedom in the structure, and use iterative solvers to enforce the joint constraints. They are much more stable than rigid bodies with joints, but are still not guaranteed to maintain joint constraints. The documentation recommends using them for use cases such as ragdolls.
* **Reduced co-ordinate articulations** These formulate the simulation directly in terms of the degrees of freedom in the structure, and so preserving joint constraints is guaranteed. The documentation recommends using them for use cases such as robotic arms. They also provide inverse dynamics functionality which is useful for robotics control systems. 

This RFC will focus on reduced co-ordinate articulations as the most suitable choice for robotic simulation.

### Representation in the engine

This section describes two possible ways to represent an articulation in O3DE.

#### EMotionFX Actor

The first approach would be to use an Actor component to represent the entire articulation.

There are some disadvantages to this approach:

* The skeleton can only be imported via FBX, so extra work would be required to handle formats typically used in robotics. 
* There is no way to edit the skeleton in the editor.
* It would often be necessary to create separate entities with attachment components in order to host other components associated with a particular link. Editing those attached entities would require going back and forth between the Animation Editor and the main Editor.
* It ties robotics to a particular animation solution. It would require the EMotionFX gem to be enabled, which might not otherwise be necessary for a robotics application. It would also interfere with the modularity of the engine, as it would make it difficult to switch out EMotionFX for a different animation solution.

#### Mapping to entities

Another approach would be to treat each link as a separate O3DE entity. This would make it straightforward to associate other components with individual links in the articulation, for example:

* Mesh components.
* Collider components.
* Sensor components.
* Joint components, used to create additional constraints between parts of the articulation (articulations must have a tree structure and cannot themselves contain loops but additional joints can be used to create looped structures).
* Articulation tendons (see below), which can also be used to create additional constraints between links.

Note that there is another RFC currently in progress to [allow disabling the inheritance of transforms from parent entities](https://github.com/o3de/sig-simulation/issues/57). If that RFC is approved, treating each link in an articulation as an entity will provide a simple workflow for authoring the structure of the articulation using the entity hierarchy. If the transform inheritance RFC is not approved, the structure could be authored using a field on each entity which identifies the entity which is its parent within the articulation structure, although that would be more cumbersome, harder to visualize and would require handling to prevent cycles.

There are some properties which apply only to the root of the articulation (such as a flag in the simulation indicating that the root is static) or which apply to the articulation as a whole (such as solver iteration counts). These can be handled by traversing the hierarchy to determine which entity is the root and only displaying those properties on that entity. Other solutions for identifying the root more explicitly were considered (for example having a separate **Articulation Root Component** or checkbox on the component to label it as a root), but it was decided they would involve unnecessary effort from the user and complication. 

Internally to PhysX, an articulation is a single object. Therefore at runtime the proposed design is for the root entity to own the whole articulation. The other link entities can hold references to the articulation owned by the root in order to handle queries or requests for each link. A custom prefab processor will be used to gather all the information that the root entity requires in order to create the whole articulation.

#### Preferred Approach

Given the disadvantages with the first approach, the preferred solution is to use an individual entity to represent each link in the articulation.

### New components

This work would introduce the following new components:

* **Articulation Link Component** Editor component holds information about the rigid body properties of the link (mass, inertia etc.), the joint type (fixed, hinge, prismatic, etc.), joint properties (limits, stiffness, damping, friction, etc.). Runtime component provides interfaces for querying simulation data, applying forces and torques, joint drives, etc.
* **Articulation Sensor Component** These can be used to query the forces and torques acting on a link. An example use case for this would be allowing a robot control system to avoid forces which could damage the robot.
* **Articulation Tendon Component** These can be used to create additional constraints between 2 articulation links.

### Custom Prefab Processor

The custom prefab processor will traverse the prefab and find all the connected entities with link components, deduce their relationship within the articulation hierarchy based on the entity hierarchy, assign link identifiers to each link, collect all the information required to create the articulation (rigid body settings, collider settings, tendon and sensor settings etc.), and attach that information to the runtime configuration of the root entity. The runtime configurations for non-root entities will just need a reference to the root entity and their link identifier.

The root will be deduced by traversing up parent links in the entity hierarchy until an entity is found which either does not have a parent or whose parent does not have an Articulation Link component. If there are intermediate entities without Articulation Link components, they will cause multiple disconnected articulations to be created. A warning could be issued in that case if appropriate.

## What are the advantages of the feature?

Greatly improved stability when simulating complex jointed structures.

## What are the disadvantages of the feature?

None

## Are there any alternatives to this feature?

One alternative is to continue using joints, but that solution has many shortcomings described above.

## How will users learn this feature?

Documentation and tutorial / demo content

## Are there any open questions?

---

![articulations (3)](https://user-images.githubusercontent.com/82226198/221664431-679f5d5a-115d-4c64-8a38-a3a09f26aed2.svg)