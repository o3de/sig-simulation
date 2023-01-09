# Physics LKG Workflow Tests

Testing in this area should focus on the Physics functionality found in PhysX and other supported Physics libraries.

## Common Issues to Watch For

Test guidance will sometimes note specific issues to watch for. The common issues below should be watched for through all testing, even if unrelated to the current workflow being tested.
- Asset processor errors when loading/saving levels with PhysX Entity Components.
- Warnings or Errors that appear in the Editor Console Log while setting up scenes. 
- Broken PhysX behaviors.
- Colliders not being detected between collider types.
- Deprecated Script Canvas nodes.

## Supported Component Variants

### PhysX Collider shapes

* Box
* Capsule
* Sphere
* PhysicsAsset
* Cylinder

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
* Prismatic Joint

### PhysX Character Controller shapes

* Capsule
* Box

## Workflows

### Area: PhysX Colliders & PhysX Shape Colliders

**Project Requirements** 

Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics

**Platforms**
* Windows
* Linux

**Docs** 
* [O3DE PhysX Collider](https://www.o3de.org/docs/user-guide/components/reference/physx/collider/)
* [O3DE PhysX Shape Collider](https://www.o3de.org/docs/user-guide/components/reference/physx/shape-collider/)

**Product:** A scene that has multiple entity scenarios that interact and collide with each other as expected when ran in Editor Game mode or Game Launcher.

**Suggested Time Box:** 75 minutes

| Workflow                                                           | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Things to Watch For                                                                                                                                                                                                                                                                                                |
|--------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PhysX Colliders collide with expected entities                     | <ol><li>Create an entity for each of the [PhysX Collider shape(s)](#physx-collider-shapes) or [PhysX Shape Collider shape(s)](#physx-shape-collider-shapes) under test.</li> <li>Add a PhysX Collider or PhysX Shape Collider with their partner [Shape](#PhysX-Shape-Collider-shapes)<li>Set up a scene in which entities with colliders will collide onto each other.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>PhysX Colliders components are successfully added to entities.</li><li>PhysX Shape Colliders alert users to the need for corresponding shape meshes.</li><li>PhysX Collider with PhysicsAsset shape works when a mesh asset is applied.</li><li>Colliders collide as expected when colliding with entities.</li> |
| Collision Groups & Layers Collide according to their matched rules | <ol><li>In the PhysX Configuration Tool create a secondary collision layer and secondary collision group.</li><li>Create enough entities with colliders set up a scene with the desired combinations of Groups and Layer collisions under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.<ol>                                                                                                                                                    | <ul><li>PhysX Configuration tool opens without issue.</li><li>New Layers and Groups can be assigned via the Layers & Collides with Collider properties.</li><li>PhysX Colliders collide with expected entities according to their Layers & Collision Groups.</li></ul>                                             |
| PhysX Materials can be applied to colliders and impact their collisions  | <ol><li>Create a PhysX Collider and PhysX Shape Collider entity setup with appropriate Collider componenets for each PhysX Material under test.</li><li>Add the desired PhysX Material(s) under test to the entities created.</li><li>Set up the scene so that the entities with PhysX Materials under test will collide with something.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol>                                                     | <ul><li>PhysX Colliders can have PhysX Materials applied.</li><li>PhysX Material properties are applied to Colliders and impact their collisions.</li>                                                                                                                                                                   |
| PhysX colliders can have their offset and contact offset modified and their collision behavior is impacted accordingly | <ol><li>Set an offset and contact offset for PhysX colliders with all supported [PhysX Collider shape(s)](#physx-collider-shapes).</li><li>Set up a scene in which created PhysX colliders with modified offsets and contact offsets collide with PhysX Rigid Bodies moved by PhysX forces.</li><li>Set a contact offset and translation offset for for each of [PhysX Shape Collider shape(s)](#physx-shape-collider-shapes) by using flag enabled Translation Offset (translation offset can be enabled by adding _"EnableShapeComponentTranslationOffset": true_ under _"Amazon" -> "Preferences"_ in _editorpreferences.setreg_ file).</li><li>Set up a scene in which created PhysX Shape colliders with modified shape translation offsets and contact offsets collide with PhysX Rigid Bodies moved by PhysX forces.</li></ol> | <ul><li>PhysX Colliders can have their offsets and contact offsets modified.</li><li>PhysX Shape colliders can have their contact offsets and flag enabled shape translation offsets modified.</li><li>PhysX colliders and PhysX shape colliders with offsets modified as described above collide with PhysX rigid bodies and other PhysX entities during simulations.</li></ul>                                                     
---

### Area: PhysX Force Region

**Project Requirements** 

Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics

**Platforms**
* Windows
* Linux

**Docs** [O3DE PhysX Force Region](https://www.o3de.org/docs/user-guide/components/reference/physx/force-region/)

**Product:** A scene with force region entities that apply forces to target entities as expected. 

**Suggested Time Box:** 60 minutes

| Workflow                                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Things to Watch For                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Force Region Types                        | <ol><li>Create an entity with a Force Region Component for each [PhysX Force Region Force Type](#PhysX-Force-Region-Force-Types) under test and configure it for the PhysX Force Regions under test using a default PhysX Collider as the collider dependency.<li>Modify the Force Region's Force Type properties. (**EG:** Setting a Magnitude and X,Y,Z directions for World Space)<ul><li>Test further by testing the boundaries defined in the docs.</ul></li><li>Set up the scene so that an entity will collide with the Force Region under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>Force Region Can be applied to an entity.</li><li>If dependent components are missing, the Force Region component notifies the user and gives them a resolution path.</li><li>Force Region types can be added to a force region and their properties can be modified.</li><li>Entities that collide with the Force Region will react according to the the expected Force applied.</li><ul><li>Force Regions apply forces in all vectors.</li><li>Force Region Net Forces are as expected.</li><li>Force Regions do not cause entities to quiver.</li></ul></ul> |
| Force Region Shapes                       | <ol><li>Rerun above scenario but swap out default PhysX Collider component to support either the [PhysX Collider shape](#physx-collider-shapes) or [PhysX Shape Collider](#PhysX-Shape-Collider-shapes) under test.</li><li>Add the desired [PhysX Force Region Force Type(s)](#PhysX-Force-Region-Force-Types) under test.</li><li>Set up the scene so that an entity will collide with the Force Region under test.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol>                                                                                                                                   | <ul><li>Force Region Can be applied to Entity with desired PhysX Collider shape or PhysX Shape Collider w/ desired shape.</li><li>Entities that collide with the Force Region will react according to the the expected Force applied.</li></ul>                                                                                                                                                                                                                                                                                                                         |
---

### Area: Terrain layers with PhysX Heightfield Colliders

**Project Requirements**

Any project that has the following Gems enabled:

* PhysX
* Primitive Assets
* Script Canvas Physics
* Terrain

**Platforms**
* Windows
* Linux

**Docs**
* [O3DE Terrain](https://www.o3de.org/docs/learning-guide/tutorials/environments/create-terrain-from-images/)

**Product:** A scene that has a Terrain Layer Spawner entity which is able to interact with PhysX entities.

**Suggested Time Box:** 20 minutes

| Workflow                                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Things to Watch For                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Creating a Terrain Layer Spawner entity with PhysX Heightfield Collider                       | <ol><li>Add Terrain World Renderer and Terrain World components to Level prefab entity. Create an entity with following components:</li><ul><li>Terrain Layer Spawner</li><li>Axis Aligned Box Shape</li><li>PhysX Heightfield Collider</li><li>Terrain Physics Heightfield Collider</li><li>Terrain Height Gradient list</li></ul></ul></li><li>Assign Terrain Height Gradient list component with a gradient.</li><li>Set up a scene in which meshed PhysX Rigid body entity collides with generated terrain layer in Editor Game Mode or in the Game Launcher.</li><li> Assign Terrain Physics Heightfield Collider with different Physics Materials and collide it with various PhysX entities.</li></ol>                                                                                                                                   | <ul><li>Terrain layer spawner entity with PhysX Heighfield Collider can be created in Editor.</li><li>Assigned gradients impact the appearance of Terrain layer spawners and their colliders.</li><li>Terrain layer spawner entity with assigned colliders correctly collides and interacts with PhysX entities.</li><li>Assigned Physics materials impact the collision behavior of Terrain layer spawner entity.</li></ul>                                                                                                                                                                                                                                                                                                                         |
---

### Area: PhysX Character Controller

**Project Requirements**

Any project that has the following Gems enabled:

* PhysX
* Primitive Assets
* Script Canvas Physics

**Platforms**
* Windows
* Linux

**Docs**
* [O3DE PhysX Character Controller](https://www.o3de.org/docs/user-guide/components/reference/physx/character-controller/)
* [O3DE PhysX Character Gameplay](https://www.o3de.org/docs/user-guide/components/reference/physx/character-gameplay/)

**Product:** A scene that has a simulating character controller entity.

**Suggested Time Box:** 30 minutes

| Workflow                                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Things to Watch For                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Creating an entity with PhysX Character Controller component    | <ol><li>Create an entity with PhysX Character Controller for each of the [PhysX Character Controller shape(s)](#physx-character-controller-shapes), add a mesh component to it for visibility.<li>Create a meshed PhysX Rigid body entity with Initial Linear velocity value other than **0** and set up a scene in which it collides with PhysX Character Controller in Editor Game Mode or in the Game Launcher.</ul></li><li>Assign PhysX Character Controller with various Physics Materials and collide it with PhysX Rigid bodies.</li></ol> | <ul><li>PhysX character controller as a kinematic controller is not affected by gravity and outside forces but can still collide with other PhysX entities.</li><li>PhysX character controller can be assigned with all available shapes and these shapes dictate how other entities collide with PhysX Character Controller.</li><li>Shapes assigned to PhysX Character Controller can be modified with options found in the component.</li></ul>
| Adding PhysX Character Gameplay component to PhysX Character Controller entity | <ol><li>Add a PhysX Character Gameplay component to PhysX Character Controller entity.</li><li>Change component values of PhysX Character Gameplay. For instance set Gravity multiplier to **0.1** and Ground Detection Box Height to **1.0** </li><li>Set up a scene in which PhysX Character Controller entity with PhysX Character Gameplay is above any PhysX collider and Enter the Game Mode.</li></ol>                                                                                                                                   | <ul><li>PhysX Character Gameplay adds non-kinematic game logic properties to PhysX Character Controller such as gravity.</li><li>Modifying component values found inside PhysX Character Gameplay impacts behavior of PhysX Character Controller entity during simulation. For instance reducing Gravity multiplier decreases the gravity force impacting PhysX character controller </li></ul>                                                                                                                                                                                                                                                                                                                         |
---

### Area: PhysX Joints

**Project Requirements**

Any project that has the following Gems enabled:

* PhysX
* Primitive Assets
* Script Canvas Physics

**Platforms**
* Windows
* Linux

**Docs**
* [O3DE PhysX Ball Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/ball-joint/)
* [O3DE PhysX Fixed Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/fixed-joint/)
* [O3DE PhysX Hinge Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/hinge-joint/)

**Product:** A scene with PhysX Joint entities that test their ability to interact with a scene.

**Suggested Time Box:** 50 minutes

| Workflow                                              | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Things to Watch For                                                                                                                                                                                                                                                               |
|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adding joints to a scene and verifying their behavior | <ol><li>Create a follower and leader entity for each [PhysX Joint Type](#PhysX-Joint-Types) under test.</li><ul><li>Follower entity should have PhysX Collider, PhysX Rigid Body, [PhysX Joint Type](#PhysX-Joint-Types), and a Shape (for visibility).</li><li>Lead entity should have a PhysX Collider, Rigid body, and a Shape (for visibility) component.</li></ul></li><li>Set up the follower to move around the joint by setting a velocity on the Rigid Body.</li><li>Set up the Joint to set the Lead entity as the Lead target.</li><li>Set up the Joint Properties under test (EG: breakable, limits).</li><li>Set up the scene so that joints will interact upon themselves or with other collider entities.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>When adding a joint to an Entity without dependent components, joint component will notify and suggest entities to add.</li><li>Joint movements are within expected limits.</li><li>Joints behave as expected when colliding with themselves or other entities.</li></ul> |
---

### Area: PhysX Rigid Body

**Project Requirements**

Any project that has the following Gems enabled:
* PhysX
* Primitive Assets
* Script Canvas Physics

**Platforms**
* Windows
* Linux

**Docs** [O3DE PhysX Rigid Body](https://www.o3de.org/docs/user-guide/components/reference/physx/rigid-body/)

**Product:** A scene with simulating PhysX Rigid Bodies entities acting according to set values.

**Suggested Time Box:** 60 minutes

| Workflow                                    | Requests                                                                                                                                                                                                             | Things to Watch For                                                                                                                                                                                                                                                                                        |
|---------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Changing Rigid Body Properties in Workflows | <ol><li>In any of the workflows with a Rigid Body found on this page change the rigid body properties for the scenario under test.</li><li>Create a meshed Rigid body entity with each of the supported [PhysX Collider shape(s)](#physx-collider-shapes) or [PhysX Shape Collider shape(s)](#physx-shape-collider-shapes), position it above the terrain/any PhysX collider and change gravity and time steps values in System and Scene Configuration settings of PhysX Configuration (PREVIEW) tool .</li><li>Enable CCD in PhysX Configuration (PREVIEW) tool and set up a scene in which two or more CCD enabled PhysX rigid bodies collide with each other by using Initial linear velocities with values greater than 50.</li><li>Enable/Disable gravity enabled toggle in PhysX Rigid Body.</li><li>Enable/Disable start asleep toggle in PhysX Rigid Body.</li><li>Set sleep threshold for PhysX Rigid body and set up a scene in which PhysX Rigid body with set sleep treshold is impacted by other PhysX Rigid body.</li><li>Run the Simulation in the Editor Game Mode or in the Game Launcher after providing any of the changes mentioned above to PhysX Rigid body entity.</li></ol> | <ul><li>Scene behaves according to the changes made to PhysX Rigid Body entity.</li><li>Time step and gravity values set in PhysX Configuration (PREVIEW) tool correctly impact behaviors of rigid bodies in a scene.</li><li>CCD enabled Rigid bodies have better hit detection during scenarios involving big velocities.</li><li>Rigid bodies with gravity enabled toggle turned off do not react to gravity set in PhysX Configuration (PREVIEW)</li><li>Rigid bodies with start asleep toggle turned on have to be woken up by a force to start simulating.</li><li>Rigid bodies with set threshold go to sleep when applied forces are lower than set threshold.</li><li>Extra care should be tested around the following Rigid Body Properties:</li><ul><li>Angular & Linear damping</li><li>COM</li><li>Kinematic Mode</li><li>Mass</li><li>Angular Velocity</li><li>Compute Intertia</li></ul></ul> |
---

### Area: Physics Assets

**Project Requirements**

Any project that has the following Gems enabled:

* PhysX
* Primitive Assets
* Script Canvas Physics
* Scene Processing

**Platforms**
* Windows
* Linux

**Docs**
* [O3DE PhysX Assets](https://www.o3de.org/docs/learning-guide/tutorials/assets/physx-colliders/)

**Product:** A scene with a few .fbx based assets with assigned and modified physxmeshes.

**Suggested Time Box:** 30 minutes

| Workflow                                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Things to Watch For                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Importing an .fbx asset into engine and generating physxmesh for it    | <ol><li>Import and find a mesh .fbx asset in Asset Browser.<li>Select and open the.fbx asset with Fbx Settings tool.</ul></li><li>Save the asset physxmesh as a Convex, Primitive and Triangle mesh.</li><li>Use the generated PhysX mesh as a PhysicsAsset shape for PhysX Colliders.</li></ol> | <ul><li>.fbx assets can be imported into project and are successfully processed by the Asset Processor.</li><li>A physxmesh is generated for an asset when its imported into the project.</li><li>Imported asset can be found in Asset Browser and can be opened with Fbx Settings tool.</li><li>Physxmesh of the asset can be saved as Convex, Primitive and Triangle mesh.</li><li>Saved physxmesh can be used as  a PhysicsAsset shape for PhysX Colliders.</li><li>Triangle meshes are the most accurate meshes but can not be used as non-kinematic rigid bodies.</li><li>All of used physxmeshes are able to generate working colliders for PhysX Collider entity.</li></ul>
| Modifying physxmeshes in Fbx Settings | <ol><li>Assign physxmeshes with various Physics Materials.</li><li>Merge physxmeshes.</li><li>Decompose Convex and Primitive physxmeshes.</li><li>Modify all of Convex, Primitive and Triangle physxmeshes asset parameters.</li></ol>                                                                                                                                   | <ul><li>Physxmeshes can be assigned with various Physics materials and their properties are reflected when entity with corresponding physxmesh is simulating in Editor.</li><li>PhysX meshes can be merged and decomposed.</li><li>Asset parameters of physxmeshes can be modified and introduced changes are reflected in physxmesh appearance and behaviors.</li></ul>                                                                                                                                                                                                                                                                                                                         |
---

### Area: Prefabs made out of PhysX entities

**Project Requirements**

Any project that has the following Gems enabled:
* PhysX
* Primitive Assets
* Prefab Builder
* Script Canvas Physics

**Platforms**
* Windows
* Linux

**Docs**
* [O3DE Prefabs](https://www.o3de.org/docs/learning-guide/tutorials/entities-and-prefabs/entity-and-prefab-basics/)

**Product:** A scene that has a working prefabs and nested prefabs made out of PhysX entities.

**Suggested Time Box:** 30 minutes

| Workflow                                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Things to Watch For                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Creating a prefab out of PhysX entities   | <ol><li>Create prefabs out of entities derived from every PhysX component.<li>Create nested prefabs out of entities with PhysX components.</li><li>Run the Simulation in the Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>Prefabs can be made out of basic and complex PhysX entities combining multiple components such as meshed rigid bodies with colliders or PhysX ragdolls with actors.</li><li>Nested prefabs can be made out of basic and complex PhysX entities combining multiple components such as meshed rigid bodies with colliders or PhysX ragdolls with actors.</li><li>PhysX simulation of prefab behaviors corresponds to behaviors expected from entities which prefabs are made of.</li></ul>
| Editing and instantiating prefabs made out of PhysX Entities  | <ol><li>Instantiate prefabs made out of PhysX entities in multiple levels.</li><li>Edit prefabs, nested prefabs and entities found in them.</li><li>Duplicate and deattach prefabs.</li></ol>                                                                                                                                   | <ul><li>Created prefabs and nested prefabs can be instantiated in various project levels and their behavior remains as expected.</li><li>Editing entities found in prefabs correctly overwrites the prefabs, including already instantiated prefabs found in Viewport.</li><li>Prefabs and nested prefabs can be edited, deattached and duplicated.</li></ul>                                                                                                                                                                                                                                                                                                                         |
---

### Area: Physics Script Canvas

**Project Requirements**

AutomatedTesting Project

**Platforms**
* Windows
* Linux

**Docs**
* [O3DE Script Canvas](https://www.o3de.org/docs/user-guide/scripting/script-canvas/)
* [O3DE Spawning with Script Canvas](https://www.o3de.org/docs/learning-guide/tutorials/entities-and-prefabs/spawn-a-prefab/)

**Product:** Various scenes in which PhysX entities perform Script Canvas driven actions.

**Suggested Time Box:** 50 minutes

| Workflow                                                           | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Things to Watch For                                                                                                                                                                                                                                                                                                |
|--------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Execute Physics AutomatedTesting test levels manually              | <ol><li>Open the AutomatedTesting Physics Script Canvas test level that's under test.</li><li>Levels are found under Physics folder with a naming pattern starting with_ScriptCanvas\_<TestScenario>_.</li><li>Run the Simulation in Editor or in the Game Launcher.</li></ol> | <ul><li>Level opens without error.</li><li>Level completes the simulation and the Editor Console/Game Log does not have any Script Canvas errors.</li><li>Level completes the simulation and the Console/Game Log has the expected Script Canvas messages from the level under test.</li> |
| Apply scripts that control input driven movement of PhysX entities | <ol><li>Create an input controllable PhysX Rigid Body entity which movement is driven by Script Canvas.</li><li>Create an input controllable PhysX Character Controller entity which movement is driven by Script Canvas.<ol>                                                                                                                                                    | <ul><li>Entity based on PhysX Rigid Body, PhysX Collider, Input and Script Canvas with script handling the entity movement can traverse the level and collide/interact with other PhysX entities.</li><li>Entity based on PhysX Character Controller, PhysX Collider, Input and Script Canvas with script handling the entity movement can traverse the level and collide/interact with other PhysX entities.</li></ul>                                             |
| Apply scripts based on trigger events                              | <ol><li>Create a trigger entity based on PhysX Collider and PhysX Shape Collider and create script that prints message in the console when the trigger collider is entered.</li><li>Create various script based effects triggered by interactions with trigger colliders with emphasis on:</li><ul><li>Spawning entities when trigger colliders are entered/exited</li><li>Changing entities transform when trigger areas are entered/exited</li><li>Applying forces and waking entities when trigger colliders are entered/exited</li></ul></ul>                                                    | <ul><li>Trigger colliders based on PhysX Collider and PhysX Shape Colliders are able to spawn entities and trigger script canvas driven actions when colliders are triggered by events such as entering or exiting.</li>                                                                                                                                                                   |
| Apply scripts that spawns PhysX entities on a level                | <ol><li>Create a script canvas driven entity that spawns prefabs in the level using prefab spawning nodes.</li><li>Create a script that spawns prefabs when certain conditions are met, such as trigger collider is entered or specific time has elapsed.</li></ol>                                                     | <ul><li>Prefabs can be created out of PhysX entities and can be spawned by Script Canvas.</li><li>Prefabs are able to be spawned when scripted conditions are met.</li>                                                                                                                                                                   |
| Apply scripts that use Raycast, shape cast and overlap nodes       | <ol><li>Create a script canvas driven entities that shoot raycasts and returns message to the console when entities are hit by it.</li><li>Create a script canvas driven entities that shoot shape casts based on all available shapes and returns message to the console when entities are hit by it.</li><li>Create a script canvas driven entities that uses overlap nodes to returns message to the console when entities are overlapping.</li><li>Create a script canvas scripts that trigger actions such as entity spawning when Raycast, Shape casts and overlap are interacting with other entities.</li></ol> | <ul><li>Raycasts, overlap and shape casts based on all available shape nodes are able to be scripted and executed by Script Canvas.</li><li>Raycasts, overlap and shape casts based on all available shape nodes are able to trigger Script Canvas driven actions when other entities are hit by a cast or overlapped with.</li></ul>                                                     
---

### Area: PhysX Cloth

**Project Requirements** 

Any project that has the following Gems enabled: 
* PhysX 
* Primitive Assets 
* Script Canvas Physics
* NvCloth
* Mesh or Actor with a Cloth data node


**Platforms**
* Windows
* Linux

**Docs** 
* [O3DE Nvidia Cloth](https://www.o3de.org/docs/user-guide/components/reference/physx/cloth/)

**Product:** A scene with Nvidia Cloth entities (Mesh/Actor) that dynamically animate when rendered in a simulation.

**Suggested Time Box:** 60 minutes

| Workflow                                                        | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Things to Watch For                                                                                                                                                                                                                                                                                                             |
|-----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Create cloth blinds and get them to render in the scene        | <ol><li>Add an entity with a Cloth and a Mesh cloth under test.</li><li>Add your desired Mesh with a Cloth data node.<ul><li>NvCloth Provides:</li><ul><li>Cloth Blinds</li><li>Cloth Blinds Broken</li><li>Cloth Locked Corners Four</li><li>Cloth Locked Corners Two</li><li>Cloth Locked Edge</li></ul></ul></li><li>On the Cloth component set your desired mesh node.</li><li>Set your desired Cloth Component Properties.</li><li>Set up your scene to view the cloth when the simulation is rendered.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol>                              | <ul><li>When adding a Cloth Component to an Entity without dependent components, component will notify and suggest entities to add.</li><li>Cloth renders and moves according to the properties set.</li><li>Cloth will Simulate in Editor when property is Enabled.</li></ul>                                                  |
| Create cloth supported Actor and get it to render in the scene | <ol><li>Add an entity with a Cloth, Actor, and Simple Motion for each cloth actor under test.</li><li>Add the Cloth Supported Actor to the Actor Component.<ul><li>Chicken Actor provided in NvCloth Gem</li></ul></li><li>Add an animation for the selected Actor to the Simple Motion component and set it to loop.<ul><li>NvCloth gem provides chickenflapping, chickenwalking, and chickenidle</li></ul></li><li>Set desired Cloth component properties.</li><li>Set up your scene to view the Actor when the simulation is rendered.</li><li>Run the Simulation in Editor Game Mode or in the Game Launcher.</li></ol> | <ul><li>When adding a Cloth Component to an Entity without dependent components, component will notify and suggest entities to add.</li><li>Cloth renders and moves according to the properties set.</li><li>Cloth will animate when the Actor moves.</li><li>Cloth will Simulate in Editor when property is Enabled.</li></ul> |
---

## Additional Coverage: New Features, Feature Improvements, Areas of Concern for Current LKG
This section should change for each LKG cycle to target new features, feature area improvements, or an area that has been presenting issues and can use additional coverage in the LKG cycle.

Execute the following Workflow Docs
* [Physics Ragdoll](physics-ragdoll-workflows.md)
* [Physics Blast Materials](physics-blast-materials-workflows.md)

**Please note that as of 12/07/2022 Blast gem was removed from o3de repo and moved to experimental branch in o3de.extras. This is due to Blast gem not being supported by PhysX5 yet. The Nvidia disclosed that Blast for PhysX 5 is planned for release on January 2023)  Once it's released the Blast is likely to return to o3de and could be tested.**
