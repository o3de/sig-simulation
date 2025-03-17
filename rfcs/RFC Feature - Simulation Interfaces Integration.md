# Introduce Simulation Interfaces to ROS 2 Gem 
<!-- This RFC template should be used for any feature that is not a bug or a substantial reorganization of the O3DE product.

If you submit a pull request to implement a new feature without going through the RFC process, it may be closed with a polite request to submit an RFC first.

A hastily-proposed RFC can hurt its chances of acceptance. Low quality proposals, proposals for previously-rejected features, or those that don't fit into the near-term roadmap, may be quickly rejected, which can be demotivating for the unprepared contributor. Laying some groundwork ahead of the RFC can make the process smoother.

Although there is no single way to prepare for submitting an RFC, it is generally a good idea to pursue feedback from other project developers beforehand, to ascertain that the RFC may be desirable; having a consistent impact on the project requires concerted effort toward consensus-building.

The most common preparations for writing and submitting an RFC include talking the idea over on our Discord server, discussing the topic on our GitHub RFCs discussions page, and occasionally posting "pre-RFCs" on the GitHub RFCs discussion page. You may file issues in the RFCs repo for discussion, but these are not actively looked at by the teams.

As a rule of thumb, receiving encouraging feedback from long-standing project developers, and particularly members of the relevant sub-team is a good indication that the RFC is worth pursuing. -->

### Summary:
<!-- Single paragraph explanation of the feature -->

This RFC is a follow-up to an effort at [ros-simulation/simulation_interfaces](https://github.com/ros-simulation/simulation_interfaces/pull/1) to standardize [ROS 2](https://docs.ros.org/en/jazzy/index.html) simulation interfaces for robotics simulators. Current implementation of similar interfaces is limited to `ROS2SpawnerComponent`, that utilizes *Gazebo* messages for ROS 2 communication. The messages were marked deprecated in the latest ROS 2 release and should be superseded. Proposed approach includes three new *Components* that will fulfill the design presented in [ros-simulation/simulation_interfaces](https://github.com/ros-simulation/simulation_interfaces/pull/1), and that are planned to be developed alongside updates to simulation interfaces. Backward compatibility will be ensured for `ROS2SpawnerComponent` and `ROS2ContactSensor` through CMake configuration with the deprecation information.


### What is the relevance of this feature?
<!-- Why is this important? What are the use cases? What will it do once completed? -->

Citing [RFC-410](https://github.com/ros-infrastructure/rep/issues/410) in [ros-infrastructure/rep](https://github.com/ros-infrastructure/rep):
```
Standardization would improve user experience when using their validation, testing and ML pipelines with several simulators, or when switching between one simulator and the other as they needs come to better match another platform. It would also make it easier to benchmark simulators.
```

### Feature design description:
<!-- - Explain the design of the feature with enough detail that someone familiar with the environment and framework can understand the concept and be able to explain it to others. 
- It should include at least one end to end example of how it will be used and specific details with outlying use cases. 

- If there is any new terminology, it should be defined here. -->

The feature is an API for handling number of ROS 2 [services](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Services/Understanding-ROS2-Services.html) and [actions](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Actions/Understanding-ROS2-Actions.html).

The following terminology that was created in [RFC-410](https://github.com/ros-infrastructure/rep/issues/410) in [ros-infrastructure/rep](https://github.com/ros-infrastructure/rep):

| Term      | Description                                                                      |
| --------- | -------------------------------------------------------------------------------- |
| Spawnable | Robot or other object that can be spawned in simulation runtime.                 |
| Entity    | Spawned spawnable, it has a unique name.                                         |
| Bounds    | A volume that is defined by an axis-aligned box shape, convex hull, or a sphere. |
| NamedPose | SE3 (translation and rotation) transform with a unique name                      |
| Tag       | A string that allows filtering entities and named poses                          |


The implementation will be split into three (or more) system components:
- `ROS 2 Entities manager`: responsible for the lifetime of spawned objects, it will cache initial positions.
- `ROS 2 Named poses manager`: responsible for aggregating information in the Named Pose Game Component.
- `ROS 2 Simulator manager`: responsible for modifying the global state of the simulation (e.g., pausing, reloading).

We will decouple the implementation of those features from their ROS 2 interfaces. Every manager will expose public methods that:
- will be callable from C++,
- will be handled through dedicated ROS 2 interface and exposed as service.

The purpose of that approach is to enable testability without the need for a ROS framework and ensure the whole system can be used with any middleware in the future. 

# ROS 2 API
This section presents the detailed plan for implementation, including potential limitation.

## GetSimulationFeatures service

The simulator needs to advertise supported features via the ROS 2 service [GetSimulatorFeatures.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetSimulatorFeatures.srv)
That service in its response provides the caller with a list of features [SimulatorFeatures.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/SimulatorFeatures.msg)

The following features are the subject of this RFC:
 - SPAWNING
 - DELETING
 - NAMED_POSES
 - POSE_BOUNDS
 - ENTITY_BOUNDS
 - ENTITY_TAGS

 - ENTITY_STATE_LISTING
 - ENTITY_STATE_SETTING

 - SIMULATION_RESET
 - SIMULATION_RESET_TIME
 - SIMULATION_RESET_SPAWNED
 - SIMULATION_PAUSE

The features *STEP_SIMULATION_SINGLE*, *STEP_SIMULATION_MULTIPLE*, and *STEP_SIMULATION_ACTION* will be introduced in the next RFC since they require multiple changes in the *PhysX Gem* and *AzPhysics API*. Action [SimulateSteps.action](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/action/SimulateSteps.action) will not be supported.

Only `[.spawnable]` format will be supported for spawning (field `spawn_formats` of [SimulatorFeatures.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/SimulatorFeatures.msg)). Other formats, such as `URDF` and `SDF`, are supported only in the Editor mode in ROS 2 Gem. Spawning `SDF` and `URDF` would require support for the Game mode:
 - handling mesh importing in game mode and preparing it to use with the *Mesh Feature Processor*,
 - creating materials and texture assets in runtime,
 - PhysX (or other Physics engine) collider mesh cooking with decomposition.

Such a feature would be useful, but it is out of the scope of this RFC.

## GetSpawnables service

This service allows users to find simulated *spawnables* to spawn (place) in the simulation. \
Service definition: [GetSpawnables](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetSpawnables.srv) \
Individual *spawnables* definition: [Spawnable.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/Spawnable.msg)

We will utilize the asset catalog to find the available *spawnables*. The asset catalog contains product assets (such as `.spawnable`, `.azmodel`, `.azbuffer`). We will introduce another product asset called `.simulationinfo`. It will contain necessary data about *prefab* that can be spawned or adjusted by Simulation Interfaces. The Engine's API will be used to get product assets, namely `AZ::Data::AssetCatalogRequests::EnumerateAssets`.

The `.simulationinfo` product asset will be an XML / JSON file (similar to other product assets like `.physxmaterial`) and will contain at least the following information:
- a description (as a string),
- bounds (as a variant of the chosen bound region),
- list of tags,
- category,
- Asset ID of the corresponding prefab.

The `.simulationinfo` will be created by the asset builder (registered by ROS 2 Gem) based on the `SimulationInfo` source assets and, eventually, the corresponding prefabs. The `SimulationInfo` source asset will be a sidecar (a file with the same filename) to a prefab. It will contain:
 - a description (as a string),
 - bounds (as a variant of the chosen bound region),
 - list of tags,
 - category.

A simulation expert who wants to create a robot (or other simulation-ready asset) needs:
- design a new prefab using standard O3DE workflow, using all available components,
- save the prefab with a name e.g., `Robots/FooRobot.prefab`
- create and fill the `SimulationInfo` sidecar file as `Robots/FooRobot.SimulationInfo` using a text editor or the O3DE tooling
- export simulation with the export script and [bundle assets](https://docs.o3de.org/docs/user-guide/packaging/asset-bundler/)

A simulation interfaces user who calls the `GetSpawnables` service against GameLauncher will call the asset catalog to find all product assets of the type `.simulationinfo`. The `ROS 2 Entity manager` will aggregate found assets and will prepare a response that will contain:
   * `uri` as `spawnable://@cache@/robot/foorobot.spawnable`
   * the description copied from `Robots/FooRobot.SimulationInfo`,
   * the bound information copied from `Robots/FooRobot.SimulationInfo`,
   * tag list copied from `Robots/FooRobot.SimulationInfo`,
   * category copied from `Robots/FooRobot.SimulationInfo`.

Prepared response will be returned to the ROS 2 user.

## SpawnEntity service

Service allows spawning entities previously found with `GetSpawnables` service. \
Service definition: [SpawnEntity](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/SpawnEntity.srv)

The ROS 2 user who wants to spawn a new object in their simulation has to have a valid URI e.g.,`spawnable://@cache@/robot/foorobot.spawnable`. `ROS 2 Entities Manager` will find respective Asset ID based on the URI.

Next, the [SpawnableEntitiesDefinition](https://github.com/o3de/o3de/blob/152bc0a1851d881fe735adf54ec93e1ad7875b11/Code/Framework/AzFramework/AzFramework/Spawnable/SpawnableEntitiesInterface.h#L334-L333) interface will be utilized to create a spawn ticket and spawn entity.

**Note:** Spawning assets from the file system e.g., `spawnable://home/username/robots/FooRobot.spawnables` will not be supported.

During spawning, the spawned O3DE entities will be modified as follows:
- The name of the root entity will reflect the name given by the `name` field,
- The [TransformComponent](https://docs.o3de.org/docs/user-guide/components/reference/transform/) of the root entity will reflect the value of the `initial_pose` field,
- The [ROS2FrameComponent](https://www.docs.o3de.org/docs/user-guide/components/reference/ros2/core/ros2-frame/) will reflect the namespace the value of the `entity_namespace` field.
- A component called `SimulationInfoComponent` will be created and attached to the root entity to cache the information from `simulationinfo` and to make it easily accessible in the future.
- [TagComponent](https://www.docs.o3de.org/docs/user-guide/components/reference/gameplay/tag/) from LmbrCentral Gem will be created at root entity (if not present) and updated with a list of tags from the `simulationinfo` sidecar.

`SimulationInfoComponent` will be handling a `SimulationInfoComponentRequestBus`.
`SimulationInfoComponentRequestBus` will have a unique string key that will come from the name of spawned entity.
This approach will result in a centralized aggregation of entities' information, and will provide an API to find all spawned entities and get their states.

## GetEntities service

This service provides access to a list of all spawned entities. \
Service definition: [GetEntities](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntities.srv )

We will serve this by calling `SimulationInfoComponentRequestBus` with `AZ::EBusAggregateResults` to obtain a list of `AZ::EntityId`, that can be converted to a list of entity names.

## GetEntitiesStates service

This service allows users to get the state (speed, location, acceleration) of chosen entities. \
Service definition: [GetEntitiesStates.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntitiesStates.srv)
Individual entity state definition: [EntityState](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/EntityState.msg)

The service handling will be similar to [GetEntities](#GetEntities). Additional calls will be required to fill the state information:
 - [RigidBodyRequestBus](https://github.com/o3de/o3de/blob/79e1df3990c5554c454d68798a0f3ef94c8ae317/Code/Framework/AzFramework/AzFramework/Physics/RigidBodyBus.h) will be used to find the current twist,
 - [TransformComponentRequests](https://github.com/o3de/o3de/blob/42d375dd99b972400f9f26bfec7e3444088f3398/Code/Framework/AzCore/AzCore/Component/TransformBus.h) will be used to find the current pose,
 - [ROS2FrameBus.h](https://github.com/o3de/o3de-extras/blob/development/Gems/ROS2/Code/Include/ROS2/Frame/ROS2FrameBus.h) will be used to find the frame ID.

**Note:** Acceleration will not be filled, and will not be supported.

## SetEntityState service

This service allows modifying the state of the chosen entity. \
Service definition: [SetEntityState.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/SetEntityState.srv)

The `AZ::EntityId` will be discovered by calling `SimulationInfoComponentRequestBus` with the name provided in the request. Next, the corresponding API will be called to adjust the state of the entity:
 - [TransformComponentRequests](https://github.com/o3de/o3de/blob/42d375dd99b972400f9f26bfec7e3444088f3398/Code/Framework/AzCore/AzCore/Component/TransformBus.h) or [RigidBodyRequestBus](https://github.com/o3de/o3de/blob/79e1df3990c5554c454d68798a0f3ef94c8ae317/Code/Framework/AzFramework/AzFramework/Physics/RigidBodyBus.h) to adjust the position of the simulated entity.
 - [ROS2FrameBus.h](https://github.com/o3de/o3de-extras/blob/development/Gems/ROS2/Code/Include/ROS2/Frame/ROS2FrameBus.h) to eventually change the frame ID,
 - [RigidBodyRequestBus](https://github.com/o3de/o3de/blob/79e1df3990c5554c454d68798a0f3ef94c8ae317/Code/Framework/AzFramework/AzFramework/Physics/RigidBodyBus.h) to adjust the speed or the acceleration.

Note that serving this service is a bit tricky in O3DE.
Due to the modularity of O3DE, the entities can be moved around in several ways:
- Simulated PhysX Rigid Body,
- Kinematic PhysX Rigid Body,
- TransformCompetent (due to parent-child relation),
- TransformComponent (due to calls to EBuses),
- PhysX character component,
- PhysX ragdoll,
- PhysX articulations,
- Potential 3rd physics engine.

Some of those methods respect calls to `TranformComponentRequests`, but some of them need to call directly methods in the PhysX gem.
| Method                                  | TransformComponentRequests API | RigidBodyRequestBus API | Custom API |
| --------------------------------------- | ------------------------------ | ----------------------- | ---------- |
| Simulated PhysX Rigid Body              |                                | supports                |            |
| Kinematic PhysX Rigid Body              | supports                       |                         |            |
| TransformComponent  due to parent-child | supports                       |                         |            |
| TransformComponent (custom calls)       |                                |                         |            |
| PhysX character component               |                                |                         | TBD        |
| PhysX ragdoll                           |                                |                         | TBD        |
| PhysX articulations                     |                                |                         | supports   |

The correct API needs to be chosen to get the expected outcome it can be quite tricky.
Let us investigate moving a `Simulated PhysX Rigid Body'.
We cannot simply call :
```cpp
 AZ::TransformBus::Event(entityId, &AZ::TransformBus::Events::SetWorldTM, transform);
```
The above snippet will work fine in a setup where the entity has a PhysX rigid body component set to kinematic.
When the PhysX rigid body component is set to the simulated type, it will have no effect.
The proper way to move such an entity (respecting joints and contacts) will be:
- call `SetKinematic(true)` from RigidBodyRequestBus to stop the simulation,
- wait for SetKinematic to be effective,
- set `SetKinematicTarget` and wait for the target reached,
- set `SetKinematic(false)` and wait for effect,
- finally, call `ForceAwake` to make sure that PhysX will run a simulation of the body.

It can take multiple cycles to achieve this.

This can lead to a few problems:
 - ROS2 Gem needs to be heavily reliable on PhysX5 gem to support moving entities with articulations, and characters.
 - Some methods to adjust pose can be quite challenging.
 - Introducing a new physics engine can lead to conflicts here.

The alternative, approach would be to cycle through:
- disable physics simulation of [simulated body](https://docs.o3de.org/docs/user-guide/interactivity/physics/nvidia-physx/simulated-bodies/),
- Use `TransformBus::Events::SetWorldTM` to change location,
- enable simulation of [simulated body](https://docs.o3de.org/docs/user-guide/interactivity/physics/nvidia-physx/simulated-bodies/).

The velocity (twist) can be applied using `RigidBodyRequestBus::SetLinearVelocity` and `RigidBodyRequestBus::SetAngularVelocity`.
It will be effective against Simulated PhysX Rigid Body and Kinematic PhysX Rigid Body and should give a warning if it called against unsupported type.

The acceleration can be applied using `RigidBodyRequestBus::ApplyLinearImpulse` w.r.t object's mass.
It will be effective against Simulated PhysX Rigid Body and should give a warning if it is called against 
unsupported type.

**Note:** Currently ROS2 Gem is heavily dependent on PhysX5 (due to SDF importer). The decoupling of those needs to be done in the future.

## DeleteEntity service

This service allows despawning the previously spawned entities. \
Service definition: [DeleteEntity.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/DeleteEntity.srv)

The `AZ::EntityId` will be discovered by calling `SimulationInfoComponentRequestBus` with the name provided in the request. Next, the corresponding API will be called to despawn and remove the corresponding spawn ticket.

**Note:** This mechanism will **not** allow to delete the entities that are the part of the level prefab (e.g., prefab instantiated in Editor).

## GetEntityBounds service

This service allows to get the bounds of the previously spawned entities. \
Service definition: [GetEntityBounds.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntityBounds.srv)

The bounds for the particular entity will be obtained by calling `SimulationInfoComponentRequestBus` with the name of the entity provided in the request. The information about entity's bounds will be stored within the `.simulationinfo` product asset, and it will be generated based on the source asset data provided by the simulation engineer.

**Note:** Convex hull bounds shape will not be supported in this implementation. This subject will be cover by another RFC when necessary.

## GetEntityInfo service

This service allows to get the category, description and tags for the given entity. \
Service definition: [GetEntityInfo.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntityInfo.srv) \
Particular entity info definition: [EntityInfo.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/EntityInfo.msg)

This information will be obtained by calling `SimulationInfoComponentRequestBus` with the name of the entity provided in the request.  The information will be stored within the `.simulationinfo` product asset, and it will be generated based on the source asset data provided by the simulation engineer.

## GetNamedPoses service

This service allows to get the list of the predefined poses which are convenient to for spawning, navigation goals, etc. \
Service definition: [GetNamedPoses.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetNamedPoses.srv) \
Individual named pose definition: [NamedPose.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/NamedPose.msg)

This information will be obtained by calling a respective bus of `ROS 2 Named poses manager`. Additionally, the aggregation will be filtered based on the parameters of the call. The poses will be predefined by a simulation expert using O3DE Editor. In particular, each entity with `NamedPoseComponent` from ROS 2 Gem will be registered in the poses manager. The `NamedPoseComponent` will contain two configurable fields called `description` and `tags`. During creation of game component (`CreateGameEntity`) the `TagComponent` from `LmbrCentral` Gem will be created and fed with the list of tags. 

**Note:** `ROS 2 Named poses manager` will ensure the names are unique (O3DE does not check for the name's uniqueness).

## GetNamedPoseBounds service

This service allows to get the bounds defined in the predefined pose object. \
Service definition [GetNamedPoseBounds.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetNamedPoseBounds.srv) 

The information about the bounds will be obtained by calling a respective bus of `ROS 2 Named poses manager`. Additionally, the aggregation will be filtered based on the parameters of the call. The bounds of the pose will be predefined by a simulation expert using O3DE Editor by adding `TransformService` and dependent services `BoxShapeService` and `SphereShapeService` alongside with the `NamedPoseComponent`.

**Note:** Convex hull bounds shape will not be supported in this implementation. This subject will be cover by another RFC when necessary.

## Reset Simulation service

This service allows to reset the simulation via ROS 2 interface. \
Service definition: [ResetSimulation.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/ResetSimulation.srv)

This service will be handled by `ROS 2 Simulator manager`. It will use multiple APIs to give results.

| Scope         | Planned API and usage                                                          |
| ------------- | ------------------------------------------------------------------------------ |
| SCOPE_ALL     | `ConsoleRequestBus` and `LoadLevel` command.                                   |
| SCOPE_SPAWNED | Internal API to destroy all spawn tickets using `ROS 2 Entities manager`.      |
| SCOPE_STATE   | Move all spawned entities to initial poses cached in `ROS 2 Entities manager`. |
| SCOPE_TIME    | New call using `ROS2Bus`                                                       |

## SetSimulationState service

This service allows to set the state of the simulation (*STOPPED*, *PAUSED*, *PLAYING*, *QUITTING*). \
Service definition: [SimulationState](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/SimulationState.msg)

This service will be handled by `ROS 2 Simulator manager`.

The transition from *PLAYING* or *PAUSED* to *STOPPED* will trigger level reloading.

The transition from *PLAYING* to *PAUSED* will ask the default physics scene to be disabled. It will stop movement of all PhysX articulations, rigid bodies (both kinematic and simulated), and characters, but some animations will be played. The transition from *PAUSED* to *PLAYING* will do the opposite.

The transition from *PLAYING*, *PAUSED*, or *STOPPED* to *QUITTING* will close simulator calling `ConsoleRequestBus` with `quit` command.


The ROS 2 Simulator manager will contain the state of the simulation and perform necessary transitions.

## GetSimulationState service

This service allows to get the current state of the simulation. \
Service definition: [GetSimulationState.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetSimulationState.srv)

This service will be handled by `ROS 2 Simulator manager`. If transition is in progress (e.g. reloading level or despawning), the old state will be returned.

# Deprecated components in ROS 2 Gem

During this effort some components will be retired, and some moved outside of scope of supported and canonical repos. This is primarily caused by ROS 2 community retiring `gazebo_msgs`.

### ROS2SpawnerComponent

The `ROS2SpawnerComponent` can co-exist with managers for *Simulation Interfaces* proposed in this RFC, but ROS 2 dependencies (namely `gazebo_msgs`) will be removed from upcoming ROS 2 distribution (`Kilted Kaiju`). The component will be marked deprecated (instead of completely removed from the codebase) to make the transition to Simulation Interfaces an easier experience. The component will be hidden away from users with the *CMake variable*. This variable will be first set to *enabled* by default, and toggled to *disabled* after the deprecation period into the retirement period. Finally, the component will be completely removed from ROS 2 Gem. 

**Note:** Additional message during the *configure* step will be shown to the user if `gazebo_msgs` package is not detected, but the *variable* is set *enabled*.

Users of `Kilted Kaiju` (and newer) ROS 2 releases will be able to use the deprecated component by building the package manually in their custom ROS 2 workspace. This involves:
- cloning and building the latest [gazebo_ros_pkgs/gazebo_msgs](https://github.com/ros-simulation/gazebo_ros_pkgs/tree/3.9.0/gazebo_msgs)
- sourcing custom workspace
- setting *CMake variable* (after deprecation period)
- building ROS2 gem from source.

The deprecation period and the retirement period of the component are yet to be decided.

### ROS2ContactSensorComponent

The `ROS2ContactSensorComponent` is independent of *Simulation Interfaces* proposed in this RFC, but ROS 2 dependencies (namely `gazebo_msgs`) will be removed from upcoming ROS 2 distribution (`Kilted Kaiju`). In particular, this component uses [`ContactState` message](https://github.com/ros-simulation/gazebo_ros_pkgs/blob/3.9.0/gazebo_msgs/msg/ContactState.msg). The component will be hidden away from users with the *CMake variable*. This variable will be first set to *enabled* by default, and toggled to *disabled* after the deprecation period into the retirement period. Finally, the component will be completely removed from ROS 2 Gem. 

**Note:** Additional message during the *configure* step will be shown to the user if `gazebo_msgs` package is not detected, but the *variable* is set to *enabled*.

Users of `Kilted Kaiju` (and newer) ROS 2 releases will be able to use the deprecated component by building the package manually in their custom ROS 2 workspace. This involves:
- cloning and building the latest [gazebo_ros_pkgs/gazebo_msgs](https://github.com/ros-simulation/gazebo_ros_pkgs/tree/3.9.0/gazebo_msgs)
- sourcing custom workspace
- setting *CMake variable* (after deprecation period)
- building ROS2 gem from source.

The deprecation period and the retirement period of the component are yet to be decided.

# Conclusions

<!-- - Explain the technical portion of the work in enough detail that members can implement the feature. 

- Explain any API or process changes required to implement this feature

- This section should relate to the feature design description by reference and explain in more detail how it makes the feature design examples work.

- This should also provide detailed information on compatibility with different hardware platforms. -->

## What are the advantages of the feature?
<!-- - Explain the advantages for someone to use this feature -->
- Adoption of the simulation standard in the robotics domain in O3DE.
- Improving the user experience of robotics and ML pipelines in O3DE.
- Improvement of the ROS 2 features' testability.
- Adjustments to the end-of-life of `gazebo_msgs`.

## What are the disadvantages of the feature?
<!-- - Explain any disadvantages for someone to use this feature -->
Significant increase in the code base size for ROS 2 Gem.

### How will this be implemented or integrated into the O3DE environment?
<!-- - Explain how this will be integrated within codebase of O3DE and any special library or technical stack requirements. -->
The *Simulation Interfaces* feature will build over the ROS 2 Gem. In particular, three separate tools for managing the *spawnables*, the simulation and the named poses will be implemented as O3DE *System Components*.  The tools will be callable using O3DE bus system and via ROS 2 middleware. The latter will be decoupled from the *Simulation Interfaces* implementation to ensure the system can be used with different frameworks in the future. More details about the design are given in section [ROS 2 API](#ros-2-api).


### Are there any alternatives to this feature?
<!-- - Provide any other designs that have been considered. Explain what the impact might be to not doing this.
- If there is any prior art or approaches with other frameworks in the same domain, explain how they may have solved this problem or implemented this feature. -->
The effort needs to be taken to adjust the existing code to the deprecation of Gazebo Classic and `gazebo_msgs`. *ROS 2 Simulation Interfaces* are the upcoming simulation standard that was primarily designed by O3DE *sig-simulation* developers. It will be integrated into all major simulation engines and, therefore, should be integrated into O3DE.

The proposed approach allows for a smooth transition from the previous implementation to the standard. We are open for suggestions on the architecture of the O3DE integration.


### How will users learn this feature?
<!-- - Detail how it can be best presented and how it is used as an extension or as a standalone tool used with O3DE.
- Explain if and how it may change the approach of how individuals would use the platform and if any documentation must be changed or reorganized.
- Explain how it would be taught to new and existing O3DE users.
- Include any significant impacts to documentation such as; Required official video updates, required product screenshot updates (i.e. Editor UX changes), new documentation site sections.  -->

- Documentation in [o3de.org](o3de.org) and [docs.ros.org](https://docs.ros.org/en/jazzy/Tutorials/Advanced/Simulators/Simulation-Main.html).
- Discord social platform of O3DE.
- Posts by OpenRobotics and Robotec.ai in their social media.
- Tutorials, conferences.

### Are there any open questions?

- Handling integration with the physics engine.
- Timing of the implementation and the release.
- Avoid coupling ROS 2 Gem with PhysX5 Gem.
