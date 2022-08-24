# Physics Workflow Tests

Testing in this area should focus on the functionality of PhysX Functionality.

## Common Issues to Watch For

Test guidance will sometimes note specific issues to watch for. The common issues below should be watched for through all testing, even if unrelated to the current workflow being tested.
- Asset processor errors when loading/saving levels with PhysX Entity Components 
- Warnings or Errors that appear in the Editor Console Log while setting up scenes 
- Broken PhysX behaviors 

### Common Terms
<b>EXAMPLE Script Canvas (SC) Editor</b>: Editing tool separate from O3DE's Editor used to create and modify script canvas graph files

<b>EXAMPLE SC Graph</b>: A collection of node objects that can be modified and saved to a file.

## Workflows

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

| Workflow                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Things to Watch For                                                                                                                                                                                                                                                                                                |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Colliders Collide         | <ol><li>Create a subset of entities for the PhysX Colliders under test:<ul><li>Box</li><li>Capsule</li><li>Sphere</li><li>PhysicsAsset</li></ul></li> <li>Create a subset of PhysX Shape Colliders under test before adding their partner Shape Componenet:<ul><li>Box</li><li>Capsule</li><li>Cylinder</li><li>Polygon Prism</li><li>Quad</li><li>Sphere</li></li></ul> <li>Set up the scene with for entities with colliders to collide into each other.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>PhysX Colliders components are successfully added to entities.</li><li>PhysX Shape Colliders alert users to the need for corresponding shape meshes.</li><li>PhysX Collider with PhysicsShape works when a mesh asset is applied.</li><li>Colliders collide as expected when colliding with entities.</li> |
| Collision Groups & Layers | <ol><li>In the PhysX Configuration Tool create a secondary collision layer and secondary collision group.</li><li>Create enough entities with colliders set up a scene with the desired combinations of Groups and Layer collisions under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.<ol>                                                                                                                                                                                                                   | <ul><li>PhysX Configuration opens without issue.</li><li>New Layers and Groups can be assigned via the Layers & Collides with Collider properties.</li><li>PhysX Colliders collide with expected entities according to their Layers & Collision Groups.</li></ul>                                                  |
| Materials                 | <ol><li>Create entities setup for both a PhysX Collider and PhsyX Shape Collider.</li><li>Add the desired Material(s) under test to the entities created.</li><li>Set up the scene so that the entities with materials under test will collide with something.</li></ol>                                                                                                                                                                                                                                                                     |                                                                                                                                                                                                                                                                                                                    |


## Workflows

### Area: PhysX Force Region

#### Project Requirements
Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics

**Platforms:**
* Windows
* Linux

**Docs:** [O3DE PhysX Force Region](https://www.o3de.org/docs/user-guide/components/reference/physx/force-region/)

**Product:** A scene with multiple force region entities that apply forces to target entities as expected 

**Suggested Time Box:** 90 minutes

| Workflow                    | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | Things to Watch For |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|
| Force Region Types          | <ol><li>Create a subset of entities with a Force Region Component and configure it for the PhysX Force Regions under test using a default PhysX Collider as the collider dependency:<ul><li>World Space</li><li>Local Space</li><li>Point</li><li>Spline Follow</li><li>Simple Drag</li><li>Linear Dampening</li></ul></li><li>Modify the Force Region's Force Type properties. (**EG:** Setting a Magnitude and X,Y,Z directions for World Space,)<ul><li>Test further by testing the boundaries defined in the docs.</ul></li></ol> | <ul><li></li></ul>  |
| Force Region Shapes         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                     |
| Force Region direction test |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                     |
| Net Force                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                     |
| Quiver                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                     |
| Linear Dampening            |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                     |
| Materials                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                     |
