# Physics LKG Workflow Tests

Testing in this area should focus on the Physics functionality found in PhysX and other supported Nv Physics libraries.

## Common Issues to Watch For

Test guidance will sometimes note specific issues to watch for. The common issues below should be watched for through all testing, even if unrelated to the current workflow being tested.
- Asset processor errors when loading/saving levels with PhysX Entity Components.
- Warnings or Errors that appear in the Editor Console Log while setting up scenes. 
- Broken PhysX behaviors.
- Colliders not being detected between collider types.

## Supported Component Variants
### PhysX Collider shapes
* Box
* Capsule
* Sphere
* PhysicsAsset

### PhysX Shape Collider shapes
* Box
* Capsule
* Cylinder
* Polygon Prism
* Quad
* Sphere

### PhysX Force Region Force Types
* World Space
* Local Space
* Point
* Spline Follow
* Simple Drag
* Linear Dampening

### PhysX Joint Types
* Ball Joint
* Fixed Joint
* Hinge Joint

## LKG Workflows

### Area: PhysX Colliders & PhysX Shape Colliders

#### Project Requirements
Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics

**Platforms:**
* Windows
* Linux

**Docs:** 
* [O3DE PhysX Collider](https://www.o3de.org/docs/user-guide/components/reference/physx/collider/)
* [O3DE PhysX Shape Collider](https://www.o3de.org/docs/user-guide/components/reference/physx/shape-collider/)

**Product:** A scene that has multiple entity scenarios that interact and collide with each other as expected when ran in Editor Game mode or Game Launcher

**Suggested Time Box:** 90 minutes

| Workflow                                                           | Requests                                                                                                                                                                                                                                                                                                                                                                                                    | Things to Watch For                                                                                                                                                                                                                                                                                                |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PhysX Colliders collide with expected entities                     | <ol><li>Create a subset of entities for the [PhysX Collider shape](#physx-collider-shapes) under test.</li> <li>Create a subset of PhysX Shape Colliders under test before adding their partner [Shape](#PhysX-Shape-Collider-shapes)<li>Set up the scene with for entities with colliders to collide into each other.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>PhysX Colliders components are successfully added to entities.</li><li>PhysX Shape Colliders alert users to the need for corresponding shape meshes.</li><li>PhysX Collider with PhysicsShape works when a mesh asset is applied.</li><li>Colliders collide as expected when colliding with entities.</li> |
| Collision Groups & Layers Collide according to their matched rules | <ol><li>In the PhysX Configuration Tool create a secondary collision layer and secondary collision group.</li><li>Create enough entities with colliders set up a scene with the desired combinations of Groups and Layer collisions under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.<ol>                                                                                 | <ul><li>PhysX Configuration tool opens without issue.</li><li>New Layers and Groups can be assigned via the Layers & Collides with Collider properties.</li><li>PhysX Colliders collide with expected entities according to their Layers & Collision Groups.</li></ul>                                             |
| Materials can be applied to colliders and impact collisions        | <ol><li>Create entities setup for both a PhysX Collider and PhsyX Shape Collider for each Material under test.</li><li>Add the desired Material(s) under test to the entities created.</li><li>Set up the scene so that the entities with materials under test will collide with something.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol>                               | <ul><li>PhysX Colliders can have Materials applied.</li><li>Material properties are applied to Colliders and impact collisions.</li>                                                                                                                                                                               |
---






