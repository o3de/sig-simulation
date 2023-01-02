Physics LKG Workflow Tests
==========================

Testing in this area should focus on the Physics functionality found in PhysX and other supported Physics libraries.

Common Issues to Watch For
--------------------------

Test guidance will sometimes note specific issues to watch for. The common issues below should be watched for through all testing, even if unrelated to the current workflow being tested.

*   Asset processor errors when loading/saving levels with PhysX Entity Components.
*   Warnings or Errors that appear in the Editor Console Log while setting up scenes.
*   Broken PhysX behaviors.
*   Colliders not being detected between collider types.
*   Deprecated Script Canvas nodes

Supported Component Variants
----------------------------

### PhysX Collider shapes

*   Box
*   Capsule
*   Sphere
*   PhysicsAsset
*   Cylinder

### PhysX Shape Collider shapes

*   Box
*   Capsule
*   Cylinder
*   Polygon Prism
*   Quad
*   Sphere

### PhysX Force Region Force Types

*   World Space
*   Local Space
*   Point
*   Spline Follow
*   Simple Drag
*   Linear Dampening

### PhysX Joint Types

*   Ball Joint
*   Prismatic Joint
*   Fixed Joint
*   Hinge Joint

### PhysX Character Controller shapes

*   Capsule
*   Box

Workflows
---------

### Area: PhysX Colliders & PhysX Shape Colliders

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE PhysX Collider](https://www.o3de.org/docs/user-guide/components/reference/physx/collider/)
*   [O3DE PhysX Shape Collider](https://www.o3de.org/docs/user-guide/components/reference/physx/shape-collider/)

**Product:**A scene that has multiple entity scenarios that interact and collide with each other as expected when ran in Editor Game mode and Game Launcher

**Suggested Time Box:**90 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| PhysX Colliders collide with expected entities | *   Create an entity for each of the[PhysX Collider shape(s)](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#physx-collider-shapes)or[PhysX Shape Collider Shape(s)](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#PhysX-Shape-Collider-shapes)under test. <br>*   Add a PhysX Collider or PhysX Shape Collider to the entity for and add their partner[Shape](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#PhysX-Shape-Collider-shapes)<br>*   Set up a scene in which entities with colliders will collider onto each other.<br>*   Run the Simulation in Editor Game Mode and in the Game Launcher. | *   PhysX Colliders components are successfully added to entities.<br>*   PhysX Shape Colliders alert users to the need for corresponding shape meshes.<br>*   PhysX Collider with PhysicsAsset shape works when a mesh asset is applied.<br>*   Colliders collide as expected when colliding with entities. |     |
| Collision Groups & Layers Collide according to their matched rules | *   In the PhysX Configuration Tool create a secondary collision layer and secondary collision group.<br>*   Create enough entities with colliders set up a scene with the desired combinations of Groups and Layer collisions under test.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher. | *   PhysX Configuration tool opens without issue.<br>*   New Layers and Groups can be assigned via the Layers & Collides with Collider properties.<br>*   PhysX Colliders collide with expected entities according to their Layers & Collision Groups. |     |
| PhysX Materials can be applied to colliders and impact collisions | *   Create a PhysX Collider and PhysX Shape Collider entity setup with appropriate Collider components for each PhysX Material under test.<br>*   Add the desired PhysX Material(s) under test to the entities created.<br>*   Set up the scene so that the entities with PhysX Materials under test will collide with something.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher.<br>*   Create new PhysX materials with the Asset Editor and verify their behavior with PhysX colliders during simulation.<br>*   Verify that Dynamic friction, Static friction, Restitution and Friction/Restitution combine values set for PhysX materials are reflected in materials behavior during simulation. | *   PhysX Colliders can have PhysX Materials applied.<br>*   PhysX Material properties are applied to Colliders and impact collisions.<br>*   New PhysX materials can be created and assigned to PhysX colliders.<br>*   Values set for PhysX materials are reflected in materials behavior during simulation. |     |
| PhysX colliders can have their offset and contact offset modified and their collision behavior is impacted accordingly. | *   Set an Offset and Contact offset for PhysX collider with all supported setup<br>*   Verify that PhysX colliders with modified Offsets and Contact Offsets correctly collide with each other according to set values.<br>*   Verify that PhysX Shape colliders can have contact offsets and translation offsets modified by using flag enabled Translation Offset for supported shapes.  <br>    <br>*   Verify that PhysX Shape colliders with modified Shape Translation Offsets and Contact Offsets correctly collide with each other according to set values. | *   PhysX Colliders can have their offsets and contact offsets modified.<br>*   PhysX shape colliders can have their contact offsets and shape translation offsets modified ( translation offset has to be enabled by adding _"EnableShapeComponentTranslationOffset": true_ under _"Amazon" -> "Preferences"_ in _editorpreferences.setreg_.  <br>    <br>*   PhysX colliders and PhysX shape colliders with offsets modified as described above correctly collide with each other during simulation. |     |

* * *

### Area: PhysX Force Region

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics

**Platforms:**

*   Windows
*   Linux

**Docs:**[O3DE PhysX Force Region](https://www.o3de.org/docs/user-guide/components/reference/physx/force-region/)

**Product:**A scene with force region entities that apply forces to target entities as expected

**Suggested Time Box:** 40 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Force Region Types | *   Create an entity with a Force Region Component for each[PhysX Force Region Force Type](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#PhysX-Force-Region-Force-Types)under test and configure it for the PhysX Force Regions under test using a default PhysX Collider as the collider dependency.<br>*   Modify the Force Region's Force Type properties. (**EG:**Setting a Magnitude and X,Y,Z directions for World Space)<br>    *   Test further by testing the boundaries defined in the docs.<br>*   Set up the scene so that an entity will collide with the Force Region under test.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher. | *   Force Region Can be applied to an entity.<br>*   If dependent components are missing, the Force Region component notifies the user and gives them a resolution path.<br>*   Force Region types can be added to a force region and their properties can be modified.<br>*   Entities that collide with the Force Region will react according to the the expected Force applied.<br><br>*   Force Regions apply forces in all vectors.<br>*   Force Region Net Forces are as expected.<br>*   Force Regions do not cause entities to quiver. |     |
| Force Region Shapes | *   Rerun above scenario but swap out default PhysX Collider component to support either the[PhysX Collider shape](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#physx-collider-shapes)or[PhysX Shape Collider](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#PhysX-Shape-Collider-shapes)under test.<br>*   Add the desired[PhysX Force Region Force Type(s)](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#PhysX-Force-Region-Force-Types)under test.<br>*   Set up the scene so that an entity will collide with the Force Region under test.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher. | *   Force Region Can be applied to Entity with desired PhysX Collider shape or PhysX Shape Collider w/ desired shape.<br>*   Entities that collide with the Force Region will react according to the the expected Force applied. |     |

* * *

### Area: Terrain layers with PhysX Heightfield Colliders

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics
*   Terrain

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE Terrain](https://www.o3de.org/docs/learning-guide/tutorials/environments/create-terrain-from-images/)

**Product:**A scene that has a Terrain Layer Spawner entity which is able to interact with PhysX entities.

**Suggested Time Box:** 20 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Creating a Terrain Layer Spawner entity with PhysX Heightfield Collider. | *   Add Terrain World Renderer and Terrain World components to Level prefab entity.<br>*   Create an entity with Terrain Layer Spawner, Axis Aligned Box Shape, PhysX Heightfield Collider, Terrain Physics Heightfield Collider and Terrain Height Gradient list components.<br>*   Assign Terrain Height Gradient list component with a gradient.<br>*   Verify the collider and collision behaviors of generated terrain layer with other PhysX entities in Editor.<br>*   Assign Terrain Physics Heightfield Collider with various Physics Materials and verify that their values are reflected in Terrain layer spawner behavior. | *   Terrain layer spawner entity with PhysX Heighfield Collider can be created in Editor.<br>*   Assigned gradient impacts the appearance of Terrain layer spawner and its collider.<br>*   Terrain layer spawner entity with assigned colliders correctly collides and interacts with PhysX entities.<br>*   Assigned Physics materials impact the collision behavior of Terrain layer spawner entity. |     |

### Area: PhysX Ragdoll

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Emotion FX Animation
*   Primitive Assets
*   Script Canvas Physics

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE Simulating Ragdoll in the Editor](https://www.o3de.org/docs/user-guide/visualization/animation/animation-editor/ragdoll/ragdoll-simulating-in-editor/)
*   [O3DE PhysX Ragdoll](https://www.o3de.org/docs/user-guide/components/reference/physx/ragdoll/)

**Product:**A scene that has a simulating PhysX Ragdoll entity.

**Suggested Time Box:** 20 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Creating an entity with PhysX Ragdoll. | *   Create an entity with Actor, Anim Graph and PhysX Ragdoll components.<br>*   Provide Actor with an asset that has ragdoll colliders assigned to its bones or add them manually<br>*   Provide anim Graph with motion set and anim graph that has activate ragdoll joints node in its graph.<br>*   Enter the game mode and make sure PhysX ragdoll is simulating. | *   PhysX ragdoll entity simulates correctly and has collision with PhysX entities when suitable assets are assigned to its components. |     |
| Assigning Physics material to PhysX Ragdoll bones. | *   Assign ragdoll colliders of actors bones with various Physics Materials.<br>*   Enter the game mode with PhysX ragdoll entity that has different materials assigned to its ragdoll colliders. | *   Behaviors of ragdoll colliders is corresponding to set materials during simulation. |     |

###   
Area: PhysX Character Controller

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE PhysX Character Controller](https://www.o3de.org/docs/user-guide/components/reference/physx/character-controller/)
*   [O3DE PhysX Character Gameplay](https://www.o3de.org/docs/user-guide/components/reference/physx/character-gameplay/)

**Product:**A scene that has a simulating character controller entity.

**Suggested Time Box:** 20 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Creating an entity with PhysX Character Controller component | *   Create an entity with PhysX Character Controller based on each supported shape, add a mesh component to it for visibility.<br>*   Collide other PhysX entities with PhysX Character Controller entity to verify its collision.<br>*   Verify behavior of collisions with PhysX Character Controller after assigning it with various Physics Materials. | *   PhysX character controller as a kinematic controller is not affected by gravity and outside forces but can still collide with other PhysX entities.<br>*   PhysX character controller can be assigned with all available shapes and these shapes dictate how other entities collide with PhysX Character Controller<br>*   Shapes assigned to PhysX Character Controller can be modified with options found in the component.  |     |
| Adding PhysX Character Gameplay component to PhysX Character Controller entity | *   Add a PhysX Character Gameplay component to PhysX Character Controller entity<br>*   Change component values of PhysX Character Gameplay<br>*   Enter Game Mode when PhysX Character Controller entity with PhysX Character Gameplay component is present in the Viewport. | *   PhysX Character Gameplay adds non-kinematic game logic properties to PhysX Character Controller such as gravity.<br>*   Modifying component values found inside PhysX Character Gameplay impacts behavior of PhysX Character Controller entity during simulation. |     |

###   
Area: PhysX Joints

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE PhysX Ball Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/ball-joint/)
*   [O3DE PhysX Fixed Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/fixed-joint/)
*   [O3DE PhysX Hinge Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/hinge-joint/)

**Product:**A scene with PhysX Joint entities that test their ability to interact with a scene.

**Suggested Time Box:** 40 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Adding joints to a scene and verifying their behavior | *   Create a follower and leader entity for each[PhysX Joint Type](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#PhysX-Joint-Types)under test.<br>*   Follower entity should have PhysX Collider, PhysX Rigid Body,[PhysX Joint Type](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-lkg-workflows.md#PhysX-Joint-Types), and a Shape (for visibility).<br>*   Lead entity should have a PhysX Collider, Rigid body, and a Shape (for visibility) component.<br>*   Set up the follower to move around the joint by setting a velocity on the Rigid Body.<br>*   Set up the Joint to set the Lead entity as the Lead target.<br>*   Set up the Joint Properties under test (EG: breakable, limits).<br>*   Set up the scene so that joints will interact upon themselves or with other collider entities.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher. | *   When adding a joint to an Entity without dependent components, joint component will notify and suggest entities to add.<br>*   Joint movements are within expected limits.<br>*   Joints behave as expected when colliding with themselves or other entities. |     |

* * *

### Area: PhysX Rigid Body

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics

**Platforms:**

*   Windows
*   Linux

**Docs:**[O3DE PhysX Rigid Body](https://www.o3de.org/docs/user-guide/components/reference/physx/rigid-body/)

**Product:**A scene with simulating PhysX Rigid Bodies entities acting according to set values.

**Suggested Time Box:**60 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Changing Rigid Body Properties in Workflows | *   In any of the workflows with a Rigid Body found on this page change the rigid body properties for the scenario under test.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher.<br>*   Change System and Scene Configuration settings in PhysX Configuration (PREVIEW) tool and verify that provided changes are reflected in rigid bodies behavior during simulation.<br>*   Enable CCD in PhysX Configuration (PREVIEW) tool and verify that rigid bodies with CCD enabled have better hit detection during fast collision scenarios.<br>*   Enable/Disable gravity enabled toggle in PhysX Rigid Body.<br>*   Enable/Disable start asleep toggle in PhysX Rigid Body.<br>*   Set sleep threshold for PhysX Rigid body and verify that entity goes to sleep when applied forces are lower than set in threshold. | *   Scene behaves according to the the Rigid Body changes made.<br>*   Time step and gravity values set in PhysX Configuration (PREVIEW) tool correctly impact behaviors of rigid bodies in a scene.<br>*   Rigid bodies with CCD enabled have better hit detection during scenarios involving big velocities.<br>*   Rigid bodies with gravity enabled toggle turned on does not react to gravity set in PhysX Configuration (PREVIEW)<br>*   Rigid bodies with start asleep toggle turned on have to be woken up by a force to start simulating<br>*   Rigid bodies with set threshold go to sleep when applied forces are lower than set threshold.<br>*   Extra care should be tested around the following Rigid Body Properties:<br><br>*   Angular & Linear damping<br>*   Linear/Angular axis locking<br>*   COM<br>*   Kinematic Mode<br>*   Mass<br>*   Angular Velocity<br>*   Compute Inertia |     |

* * *

### Area: Physics Assets

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics
*   Scene Processing

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE PhysX Assets](https://www.o3de.org/docs/learning-guide/tutorials/assets/physx-colliders/)

**Product:**A scene with a few .fbx based assets with assigned and modified physxmeshes.

**Suggested Time Box:** 30 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Importing an .fbx asset into engine and generating physxmesh for it. | *   Import and find a mesh .fbx asset in Asset Browser.<br>*   Open the selected .fbx asset with Fbx Settings tool.<br>*   Save the asset physxmesh as a Convex, Primitive and Triangle mesh.<br>*   Use the generated PhysX mesh as a PhysicsAsset shape for PhysX Colliders | *   .fbx assets can be imported into project and are successfully processed by the Asset Processor.<br>*   A physxmesh is generated for an asset when its imported into the project.<br>*   Imported asset can be found in Asset Browser and can be opened wit Fbx Settings tool.<br>*   Physxmesh of the asset can be saved as Convex, Primitive and Triangle mesh.<br>*   Saved physxmesh can be used as  a PhysicsAsset shape for PhysX Colliders.<br>*   Triangle meshes are the most accurate meshes but can not be used as non-kinematic rigid bodies.<br>*   All of used physxmeshes are able to generate working colliders for PhysX Collider entity. |     |
| Modifying physxmeshes in Fbx Settings. | *   Assign physxmeshes with various Physics Materials.<br>*   Merge physxmeshes.<br>*   Decompose Convex and Primitive physxmeshes.<br>*   Modify and verify all of Convex, Primitive and Triangle physxmeshes asset parameters. | *   Physxmeshes can be assigned with various Physics materials and their properties are reflected when entity with corresponding physxmesh is simulating in Editor.<br>*   PhysX meshes can be merged and decomposed<br>*   Asset parameters of physxmeshes can be modified and introduced changes are reflected in physxmesh appearance and behaviors. |     |

###   
  
Area: Prefabs made out of PhysX entities

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Prefab Builder
*   Script Canvas Physics

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE Prefabs](https://www.o3de.org/docs/learning-guide/tutorials/entities-and-prefabs/entity-and-prefab-basics/)

**Product:**A scene that has a working prefabs and nested prefabs made out of PhysX entities.

**Suggested Time Box:** 30 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Creating a prefab out of PhysX entities | *   Create prefabs out of entities derived from every PhysX component:<br><br>*   PhysX Colliders and PhysX Shape Colliders with all available shapes.<br>*   PhysX Ball, Fixed, Hinge and Prismatic Joints.<br>*   PhysX Force Regions<br>*   PhysX Rigid Bodies<br>*   PhysX Ragdoll<br>*   Terrain layer entities based on PhysX system<br>*   PhysX Character Controllers<br><br>*   Create nested prefabs out of entities from PhysX components listed above.<br>*   Enter Game Mode and Simulation mode in Viewport and verify that behavior of prefabs corresponds to entities which prefabs are made of. | *   Prefabs can be made out of basic and complex PhysX entities combining multiple components such as meshed rigid bodies with colliders or PhysX ragdolls with actors.<br>*   Nested prefabs can be made out of basic and complex PhysX entities combining multiple components such as meshed rigid bodies with colliders or PhysX ragdolls with actors.<br>*   PhysX simulation of prefab behaviors corresponds to behaviors expected from entities which prefabs are made of. |     |
| Editing and instantiating prefabs made out of PhysX Entities  | *   Instantiate prefabs made out of PhysX entities in multiple levels.<br>*   Edit prefabs, nested prefabs and entities found in them.<br>*   Duplicate and deattach prefabs. | *   Created prefabs and nested prefabs can be instantiated in various project levels and their behavior remains as expected.<br>*   Editing entities found in prefabs correctly overwrites the prefabs, including already instantiated prefabs found in Viewport.<br>*   Prefabs and nested prefabs can be edited, deattached and duplicated. |     |

###   
  
Area: Physics Script Canvas

**Project Requirements**AutomatedTesting Project

**Platforms:**

*   Windows
*   Linux

**Docs:**

*   [O3DE Script Canvas](https://www.o3de.org/docs/user-guide/scripting/script-canvas/)
*   [O3DE Spawning with Script Canvas](https://www.o3de.org/docs/learning-guide/tutorials/entities-and-prefabs/spawn-a-prefab/)

**Product:** Various scenes in which PhysX entities perform Script Canvas driven actions.

**Suggested Time Box:** 50 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Execute Physics AutomatedTesting test levels manually | *   Open the AutomatedTesting Physics Script Canvas test level that's under test.<br>*   Levels are found under Physics folder with a naming pattern starting with_ScriptCanvas\_<TestScenario>_.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher. | *   Level opens without error.<br>*   Level completes the simulation and the Editor Console/Game Log does not have any Script Canvas errors.<br>*   Level completes the simulation and the Console/Game Log has the expected Script Canvas messages from the level under test. |     |
| Apply scripts that control input driven movement of PhysX entities. | *   Create an input controllable PhysX Rigid Body entity which movement is driven by Script Canvas.<br>*   Create an input controllable PhysX Character Controller entity which movement is driven by Script Canvas. | *   Entity based on PhysX Rigid Body, PhysX Collider, Input and Script Canvas with script handling the entity movement can traverse the level and collide/interact with other PhysX entities.<br>*   Entity based on PhysX Character Controller, PhysX Collider, Input and Script Canvas with script handling the entity movement can traverse the level and collide/interact with other PhysX entities. |     |
| Apply scripts based on trigger events | *   Create a trigger entity based on PhysX Collider and PhysX Shape Collider and create script that prints message in the console when the trigger are is entered.<br>*   Create various script based effects triggered by interactions with trigger areas with emphasis on  <br>    <br><br>*   Spawning entities when trigger areas are entered/exited<br>*   Changing entities transform when trigger areas are entered/exited<br>*   Applying forces and waking entities when trigger areas are entered/exited. | *   Trigger areas based on PhysX Collider and PhysX Shape Colliders are able to spawn entities and trigger script canvas driven actions when areas are triggered by events such as entering or exiting. |     |
| Apply scripts that spawns PhysX entities on a level | *   Create a script canvas driven entity that spawns prefabs in the level using prefab spawning nodes.<br>*   Create a script that spawns prefabs when certain conditions are met, such as trigger area is entered or specific time has elapsed. | *   Prefabs can be created out of PhysX entities and can be spawned by Script Canvas.<br>*   Prefabs are able to be spawned when scripted conditions are met. |     |
| Apply scripts that use Raycast, shape cast and overlap nodes.  | *   Create a script canvas driven entities that shoot raycasts and returns message to the console when entities are hit by it.  <br>    <br>*   Create a script canvas driven entities that shoot shape casts based on all available shapes and returns message to the console when entities are hit by it.<br>*   Create a script canvas driven entities that uses overlap nodes to returns message to the console when entities are overlapping.<br>*   Create a script canvas scripts that trigger actions such as entity spawning when Raycast, Shape casts and overlap are interacting with other entities.  | *   Raycasts, overlap and shape casts based on all available shape nodes are able to be scripted and executed by Script Canvas.<br>*   Raycasts, overlap and shape casts based on all available shape nodes are able to trigger Script Canvas driven actions when other entities are hit by a cast or overlapped with. |     |

* * *

### Area: PhysX Cloth

**Project Requirements**

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics
*   NvCloth
*   Mesh or Actor with a Cloth data node

**Platforms:**

*   Windows
*   Linux

**Docs:**[O3DE Nvidia Cloth](https://www.o3de.org/docs/user-guide/components/reference/physx/cloth/)

**Product:**A scene with Nvidia Cloth entities (Mesh/Actor) that dynamically animate when rendered in a simulation.

**Suggested Time Box:** 40 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Create cloth blinds and get them to render in the scene. | *   Add an entity with a Cloth and a Mesh cloth under test.<br>*   Add your desired Mesh with a Cloth data node.<br>    *   NvCloth Provides:<br>    *   Cloth Blinds<br>    *   Cloth Blinds Broken<br>    *   Cloth Locked Corners Four<br>    *   Cloth Locked Corners Two<br>    *   Cloth Locked Edge<br>*   On the Cloth component set your desired mesh node.<br>*   Set your desired Cloth Component Properties.<br>*   Set up your scene to view the cloth when the simulation is rendered.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher. | *   When adding a Cloth Component to an Entity without dependent components, component will notify and suggest entities to add.<br>*   Cloth renders and moves according to the properties set.<br>*   Cloth will Simulate in Editor when property is Enabled. |     |
| Create cloth supported Actor and get it to render in the scene. | *   Add an entity with a Cloth, Actor, and Simple Motion for each cloth actor under test.<br>*   Add the Cloth Supported Actor to the Actor Component.<br>    *   Chicken Actor provided in NvCloth Gem<br>*   Add an animation for the selected Actor to the Simple Motion component and set it to loop.<br>    *   NvCloth gem provides chickenflapping, chickenwalking, and chickenidle<br>*   Set desired Cloth component properties.<br>*   Set up your scene to view the Actor when the simulation is rendered.<br>*   Run the Simulation in Editor Game Mode or in the Game Launcher. | *   When adding a Cloth Component to an Entity without dependent components, component will notify and suggest entities to add.<br>*   Cloth renders and moves according to the properties set.<br>*   Cloth will animate when the Actor moves.<br>*   Cloth will Simulate in Editor when property is Enabled. |     |

* * *

Additional Coverage: New Features, Feature Improvements, Areas of Concern for Current LKG
-----------------------------------------------------------------------------------------

This section should change for each LKG cycle to target new features, feature area improvements, or an area that has been presenting issues and can use additional coverage in the LKG cycle.

Execute the following Workflow Docs:

*   [Physics Blast Materials](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/physics-blast-materials-workflows.md) (Below)

Physics Blast Materials Workflows
=================================

**Please note that as of 12/07/2022 Blast gem was removed from o3de repo and moved to experimental branch in o3de.extras.  
****This is due to Blast gem not being supported by PhysX5 yet. The Nvidia disclosed that Blast for PhysX 5 is planned for release on January 2023)  
Once it's released the Blast is likely to return to o3de and could be tested.**

Test Approach
-------------

*   Verify that the conversion of Blast materials are successful

Blast Materials Test Workflows
==============================

Project Requirements
--------------------

Any project that has the following Gems enabled:

*   PhysX
*   Primitive Assets
*   Script Canvas Physics
*   NvCloth
*   Mesh or Actor with a Cloth data node

**Platforms:**

*   Windows
*   Linux

**Product:**A scene with Nvidia Blast entities that are able to collide with each other and shatter.

**Suggested Time Box:**30 minutes

| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| Workflow | Requests | Things to Watch For | Lionbridge Notes |
| --- | --- | --- | --- |
| **Entity Components**  <br>  <br>Verify that the blast family entity components are able to have the blast material assigned to it directly from a library of materials and a simulation can be ran. | **Prerequisite:**<br><br>*   Blast Gem has been enabled and built for the project (AutomatedTesting comes with it enabled)<br>*   A level with at least two entities has been created<br>*   Editor has been opened to the level that contains the entities<br><br>**Steps**<br><br>*   On the first entity add the Blast Family entity components<br>    *   Blast Family<br>        *   Blast Asset<br>            *   Desired Test Asset<br>        *   Choose Desired Blast Material<br>    *   Blast Family Mesh Data<br>*   On the second entity add the following PhysX components<br>*   PhysX Collider<br>*   PhysX Rigid Body<br>*   Gravity Enabled = True<br>*   Mesh (to provide visibility to the object)<br>*   Sphere.fbx or any other<br>*   Orient the scene so that the the entity with the PhysX collider will collide with the entity that contains blast family entity<br>*   Enter Game Mode (Ctrl + G) or Load Level in Game Launcher | *   User can assign the blast materials to the component<br>*   Editor and runtimes do not crash<br>*   No errors are displayed in the console<br>*   Simulation Runs |     |
| **Verify Conversion Tool Works**  <br>  <br>There will be 2 commands for conversion. Both will be an Editor console command:  <br><br>*   One to convert the blast material assets<br>*   One to convert the Prefabs that utilize blast material assets | **Prerequisite:**<br><br>*   Unconverted material library asset and prefab. Extract it in your project:[BlastOldAssets.zip](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/testassets/BlastOldAssets.zip)<br><br>**Steps**<br><br>*   Run Asset Processor and wait until all assets have been processed<br>*   Open Editor. Do not open any level, just close the "Welcome to O3DE" screen.<br>*   Open Console: Tools > Console<br>*   **Convert old blast material library asset**:<br>*   Enter the following command on the console: "ed\_blastConvertMaterialLibrariesIntoIndividualMaterials"<br>*   Expected result:<br>*   File BlastMaterialLIbrary.blastmaterial was deleted.<br>*   The following files were generated: BlastMaterial1.blastmaterial, BlastMaterial2.blastmaterial, BlastMaterial3.blastmaterial<br>*   **Fix prefabs that utilize old blast material library**:<br>*   Enter the following command on the console: "ed\_blastFixPrefabsWithBlastComponentLegacyMaterials"  <br>    Note: it might take a few seconds since it's checking all prefabs in the project.<br>*   Expected result:<br>*   Reports that BlastTest.prefab was fixed | *   Check material properties were converted correctly by opening the 3 .blastmaterial one by one with Asset Editor (Tools > Asset Editor) and check its health property. BlastMaterial1 should have health value of 1, BlastMaterial2 health 2 and BlastMaterial3 health 3.<br>*   Check prefab was fixed correctly by opening a level and dragging and dropping BlastTest.prefab into it. In Entity Outliner (Tools > Entity Outliner) edit the prefab and select its inner entity. In Entity Inspector (Tools > Entity Inspector) it must have a Blast Family component and its Blast Material field should be "blastmaterial2". |     |
| **Verify****Unconverted Blast Material Using a Level** | **Prerequisite:**<br><br>*   Unconverted Asset has been extracted to your project and processed by the asset processor:[blastdamagematerial.zip](https://github.com/o3de/sig-simulation/blob/main/testing-guidance/workflow-tests/physics/testassets/blastdamagematerial.zip)<br><br>**Steps**<br><br>*   Execute the**Entity Components**workflow using the asset(s) found in the provided zip | *   Editor and runtimes do not crash<br>*   Entity with a blast family component that utilizes an old blast material uses the default material instead and a message appears in the console log letting the user know as much when a material is assigned |     |
| **Verify a Blast Component with no Material Assigned to it** | **Prerequisite:**<br><br>*   Blast Gem has been enabled and built for the project (AutomatedTesting comes with it enabled)<br>*   A level with at least two entities has been created<br>*   Editor has been opened to the level that contains the entities<br><br>**Steps**<br><br>*   On the first entity add the Blast Family entity components<br>*   Blast Family<br>*   Blast Asset<br>*   Desired Test Asset<br>*   Leave Blast Material Blank<br>*   Blast Family Mesh Data<br>*   On the second entity add the following PhysX components<br>*   PhysX Collider<br>*   PhysX Rigid Body<br>*   Gravity Enabled = True<br>*   Mesh (to provide visibility to the object)<br>*   Sphere.fbx or any other<br>*   Orient the scene so that the the entity with the PhysX collider will collide with the entity that contains blast family entity<br>*   Enter Game Mode (Ctrl + G) or Load Level in Game Launcher | *   Editor and runtimes do not crash<br>*   No errors are displayed in the console<br>*   Default values of the material are used |     |

[Filter table data](#)[Create a pivot table](#)[Create a chart from data series](#)

[Configure buttons visibility](/users/tfac-settings.action)