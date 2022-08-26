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

| Workflow                                                           | Requests                                                                                                                                                                                                                                                                                                                                                                                                    | Things to Watch For                                                                                                                                                                                                                                                                                                |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PhysX Colliders collide with expected entities                     | <ol><li>Create a subset of entities for the [PhysX Collider shape](#physx-collider-shapes) under test.</li> <li>Create a subset of PhysX Shape Colliders under test before adding their partner [Shape](#PhysX-Shape-Collider-shapes)<li>Set up the scene with for entities with colliders to collide into each other.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>PhysX Colliders components are successfully added to entities.</li><li>PhysX Shape Colliders alert users to the need for corresponding shape meshes.</li><li>PhysX Collider with PhysicsShape works when a mesh asset is applied.</li><li>Colliders collide as expected when colliding with entities.</li> |
| Collision Groups & Layers Collide according to their matched rules | <ol><li>In the PhysX Configuration Tool create a secondary collision layer and secondary collision group.</li><li>Create enough entities with colliders set up a scene with the desired combinations of Groups and Layer collisions under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.<ol>                                                                                 | <ul><li>PhysX Configuration tool opens without issue.</li><li>New Layers and Groups can be assigned via the Layers & Collides with Collider properties.</li><li>PhysX Colliders collide with expected entities according to their Layers & Collision Groups.</li></ul>                                             |
| Materials can be applied to colliders and impact collisions        | <ol><li>Create entities setup for both a PhysX Collider and PhsyX Shape Collider for each Material under test.</li><li>Add the desired Material(s) under test to the entities created.</li><li>Set up the scene so that the entities with materials under test will collide with something.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol>                               | <ul><li>PhysX Colliders can have Materials applied.</li><li>Material properties are applied to Colliders and impact collisions.</li>                                                                                                                                                                               |
---

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

**Product:** A scene with force region entities that apply forces to target entities as expected 

**Suggested Time Box:** 60 minutes

| Workflow                                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Things to Watch For                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Force Region Types                        | <ol><li>Create a subset of entities with a Force Region Component for each [PhysX Force Region Force Type](#PhysX-Force-Region-Force-Types) under test and configure it for the PhysX Force Regions under test using a default PhysX Collider as the collider dependency.<li>Modify the Force Region's Force Type properties. (**EG:** Setting a Magnitude and X,Y,Z directions for World Space)<ul><li>Test further by testing the boundaries defined in the docs.</ul></li><li>Set up the scene so that an entity will collide with the Force Region under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>Force Region Can be applied to an entity.</li><li>If dependent components are missing, the Force Region component notifies the user and gives them a resolution path.</li><li>Force Region types can be added to a force region and their properties can be modified.</li><li>Entities that collide with the Force Region will react according to the the expected Force applied.</li><ul><li>Force Regions apply forces in all vectors.</li><li>Force Region Net Forces are as expected.</li><li>Force Regions do not cause entities to quiver.</li></ul></ul> |
| Force Region Shapes                       | <ol><li>Rerun above scenario but swap out default PhysX Collider component to support either the [PhysX Collider shape](#physx-collider-shapes) or [PhysX Shape Collider](#PhysX-Shape-Collider-shapes) under test.</li><li>Add the desired [PhysX Force Region Force Type(s)](#PhysX-Force-Region-Force-Types) under test.</li><li>Set up the scene so that an entity will collide with the Force Region under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol>                                                                                                                                              | <ul><li>Force Region Can be applied to Entity with desired PhysX Collider shape or PhysX Shape Collider w/ desired shape.</li><li>Entities that collide with the Force Region will react according to the the expected Force applied.</li></ul>                                                                                                                                                                                                                                                                                                                         |
---

### Area: PhysX Joints

#### Project Requirements
Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics

**Platforms:**
* Windows
* Linux

**Docs:** 
* [O3DE PhysX Ball Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/ball-joint/)
* [O3DE PhysX Fixed Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/fixed-joint/)
* [O3DE PhysX Hinge Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/hinge-joint/)

**Product:** A scene with PhysX Joint entities that test their ability to interact with a scene

**Suggested Time Box:** 60 minutes

| Workflow                                              | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Things to Watch For                                                                                                                                                                                                                                                               |
|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adding joints to a scene and verifying their behavior | <ol><li>Create a follower and leader entity for each [PhysX Joint Type](#PhysX-Joint-Types) under test.</li><ul><li>Follower entity should have PhysX Collider, PhysX Rigid Body, [PhysX Joint Type](#PhysX-Joint-Types), and a Shape (for visibility).</li><li>Lead entity should have a PhysX Collider, Rigid body, and a Shape (for visibility) component.</li></ul></li><li>Set up the follower to move around the joint by setting a velocity on the Rigid Body.</li><li>Set up the Joint to set the Lead entity as the Lead target.</li><li>Set up the Joint Properties under test (EG: breakable, limits).</li><li>Set up the scene so that joints will interact upon themselves or with other collider entities.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>When adding a joint to an Entity without dependent components, joint component will notify and suggest entities to add.</li><li>Joint movements are within expected limits.</li><li>Joints behave as expected when colliding with themselves or other entities.</li></ul> |
---

### Area: PhysX Rigid Body

#### Project Requirements
Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics

**Platforms:**
* Windows
* Linux

**Docs:** [O3DE PhysX Rigid Body](https://www.o3de.org/docs/user-guide/components/reference/physx/rigid-body/)

**Product:** Pass/Fail script execution.

**Suggested Time Box:** 60 minutes

| Workflow                                    | Requests                                                                                                                                                                                                             | Things to Watch For                                                                                                                                                                                                                                                                                        |
|---------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Changing Rigid Body Properties in Workflows | <ol><li>In any of the workflows with a Rigid Body found on this page change the rigid body properties for the scenario under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>Scene behaves according to the the Ridgid Body changes made.</li><li>Extra care should be tested around the following Rigid Body Properties:</li><ul><li>Angular & Linear damping</li><li>COM</li><li>Kinematic Mode</li><li>Mass</li><li>Angular Velocity</li><li>Compute Intertia</li></ul></ul> |
---

### Area: Physics Script Canvas

#### Project Requirements
AutomatedTesting Project

**Platforms:**
* Windows
* Linux

**Docs:** N/A

**Product:** A scene with PhysX Joint entities that test their ability to interact with a scene

**Suggested Time Box:** 15 minutes

| Workflow                                              | Requests                                                                                                                                                                                                                                                                                             | Things to Watch For                                                                                                                                                                                                                                                                            |
|-------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Execute Physics AutomatedTesting test levels manually | <ol><li>Open the AutomatedTesting Physics Script Canvas test level that's under test.</li><ul><li>Levels are found under Physics folder with a naming pattern starting with _ScriptCanvas\_\<TestScenario\>_.</li></ul><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>Level opens without error.</li><li>Level completes the simulation and the Editor Console/Game Log does not have any Script Canvas errors.</li><li>Level completes the simulation and the Console/Game Log has the expected Script Canvas messages from the level under test.</li></ul> |
---

### Area: PhysX Cloth

#### Project Requirements
Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics
* NvCloth
* Mesh or Actor with a Cloth data node


**Platforms:**
* Windows
* Linux

**Docs:** [O3DE Nvidia Cloth](https://www.o3de.org/docs/user-guide/components/reference/physx/cloth/)

**Product:** A scene with Nvidia Cloth entities (Mesh/Actor) that dynamically animate when rendered in a simulation.

**Suggested Time Box:** 60 minutes

| Workflow                                                        | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Things to Watch For                                                                                                                                                                                                                                                                                                             |
|-----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Create cloth blinds and get them to render in the scene.        | <ol><li>Add an entity with a Cloth and a Mesh cloth under test.</li><li>Add your desired Mesh with a Cloth data node.<ul><li>NvCloth Provides:</li><ul><li>Cloth Blinds</li><li>Cloth Blinds Broken</li><li>Cloth Locked Corners Four</li><li>Cloth Locked Corners Two</li><li>Cloth Locked Edge</li></ul></ul></li><li>On the Cloth component set your desired mesh node.</li><li>Set your desired Cloth Component Properties.</li><li>Set up your scene to view the cloth when the simulation is rendered.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol>                              | <ul><li>When adding a Cloth Component to an Entity without dependent components, component will notify and suggest entities to add.</li><li>Cloth renders and moves according to the properties set.</li><li>Cloth will Simulate in Editor when property is Enabled.</li></ul>                                                  |
| Create cloth supported Actor and get it to render in the scene. | <ol><li>Add an entity with a Cloth, Actor, and Simple Motion for each cloth actor under test.</li><li>Add the Cloth Supported Actor to the Actor Component.<ul><li>Chicken Actor provided in NvCloth Gem</li></ul></li><li>Add an animation for the selected Actor to the Simple Motion component and set it to loop.<ul><li>NvCloth gem provides chickenflapping, chickenwalking, and chickenidle</li></ul></li><li>Set desired Cloth component properties.</li><li>Set up your scene to view the Actor when the simulation is rendered.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>When adding a Cloth Component to an Entity without dependent components, component will notify and suggest entities to add.</li><li>Cloth renders and moves according to the properties set.</li><li>Cloth will animate when the Actor moves.</li><li>Cloth will Simulate in Editor when property is Enabled.</li></ul> |
---

## New Features, Feature Improvements, Added Coverage for Current LKG
This section should change for each LKG cycle to target new features, feature area improvements, or an area that has been presenting issues and can use additional coverage in the LKG cycle.

Execute the following Workflow Docs:
* [Physics Blast Materials](physics-blast-materials-workflows.md)

