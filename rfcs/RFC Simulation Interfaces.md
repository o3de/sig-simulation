# Introduce Simulation Interfaces to ROS 2 Gem 
<!-- This RFC template should be used for any feature that is not a bug or a substantial reorganization of the O3DE product.

If you submit a pull request to implement a new feature without going through the RFC process, it may be closed with a polite request to submit an RFC first.

A hastily-proposed RFC can hurt its chances of acceptance. Low quality proposals, proposals for previously-rejected features, or those that don't fit into the near-term roadmap, may be quickly rejected, which can be demotivating for the unprepared contributor. Laying some groundwork ahead of the RFC can make the process smoother.

Although there is no single way to prepare for submitting an RFC, it is generally a good idea to pursue feedback from other project developers beforehand, to ascertain that the RFC may be desirable; having a consistent impact on the project requires concerted effort toward consensus-building.

The most common preparations for writing and submitting an RFC include talking the idea over on our Discord server, discussing the topic on our GitHub RFCs discussions page, and occasionally posting "pre-RFCs" on the GitHub RFCs discussion page. You may file issues in the RFCs repo for discussion, but these are not actively looked at by the teams.

As a rule of thumb, receiving encouraging feedback from long-standing project developers, and particularly members of the relevant sub-team is a good indication that the RFC is worth pursuing. -->

### Summary:
<!-- Single paragraph explanation of the feature -->

There is an effort at [ros-simulation/simulation_interfaces](https://github.com/ros-simulation/simulation_interfaces/pull/1) to standardized simulation interfaces existing robotics simulators in [ROS 2](https://docs.ros.org/en/jazzy/index.html).


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

There is a terminology that was created in [RFC-410](https://github.com/ros-infrastructure/rep/issues/410) in [ros-infrastructure/rep](https://github.com/ros-infrastructure/rep):

|Term             |    Description                                                                                      |
|-----------------|-----------------------------------------------------------------------------------------------------|
|Spawnable        | Robot or other object which can be spawned in simulation runtime.                                   |
|Entity           | Spawned spawnable, it has unique name.
|Bound            | Region defined by axis-aligned box shape, convex hull or sphere.
|NamedPose        | SE3 (translation and rotation) transform with unique name
|Tag              | A string that allow filtering entities and named poses


The implementation will be split to three (or more system components):
- ROS 2 Entities manager \
  It will be responsible for lifetime of spawned objects, it will cache initial positions.
- ROS 2 Named poses manager \
  It will be responsible for aggregating information in Named Pose Game Component.
- ROS 2 Simulator manager \
  It will be responsible for modifying global state ofr the simulation (e.g., pausing, reloading).

We will decouple implementation of those managers from ROS 2 service.
Every manager will need to expose public methods that will be callable both from C++ and ROS 2.
Purpose of that approach is to enable testability without need for ROS domain. 

# ROS 2 API
In this section detailed plan of the implementation with potential limitations is presented.

## GetSimulationFeatures service

It needs to advertise supported features via the ROS 2 service [GetSimulatorFeatures.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetSimulatorFeatures.srv)
That service in its response provides the caller with a list of features [SimulatorFeatures.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/SimulatorFeatures.msg)

In this RFC we are proposing to support the following features:
 - SPAWNING
 - DELETING
 - NAMED_POSES
 - POSE_BOUNDS
 - ENTITY_BOUNDS

 - ENTITY_STATE_LISTING
 - ENTITY_STATE_SETTING

 - SIMULATION_RESET
 - SIMULATION_RESET_TIME
 - SIMULATION_RESET_SPAWNED
 - SIMULATION_RESET_STATE

 - SIMULATION_PAUSE

We do not plan to support the moment:
 - STEP_SIMULATION_SINGLE
 - STEP_SIMULATION_MULTIPLE
 - STEP_SIMULATION_ACTION

Following formats will be supported for spawning (field `spawn_formats` of [SimulatorFeatures.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/SimulatorFeatures.msg)):
```
[`.spawnables`]
```

**Note** Other formats e.g. `URDF` and `SDF` are supported by ROS 2 Gem, but only in Editor. 
Those tools are not available in the game mode, so spawning `SDF` and `URDF` would require:
 - handling mesh importing in Game Launcher and preparing it to use with Mesh Feature processor,
 - creating materials and texture assets in runtime,
 - PhysX (or other Physics engine) collider mesh cooking with decomposition.

Such feature, would be useful, but it is out of scope of this RFC.

Features ` SIMULATION_RESET_STATE, STEP_SIMULATION_SINGLE, STEP_SIMULATION_MULTIPLE, STEP_SIMULATION_ACTION` will be introduced in next RFC, since it requires multiple changes in PhysX gem, AzPhysics API.
Action [SimulateSteps.action](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/action/SimulateSteps.action) will not be supported.

## GetSpawnables service

This service allows users to find simulated spawnables to spawn (place) in the simulation.

Service definition : [GetSpawnables](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetSpawnables.srv)
```
# Return a list of resources which are valid as SpawnEntity uri fields (e.g. visible to or registered in simulator).
# This interface is an optional extension and might not be implemented by your simulator, check the result_code.

string[] sources                                    # Optional field for additional sources (local or remote) to search.
                                                    # By default, each simulator has visibility of spawnables through
                                                    # some mechanisms, e.g. a set of paths, registered assets etc.
                                                    # Since the simulator cannot possibly look everywhere,
                                                    # this field allows the user to specify additional sources.
                                                    # Unrecognized values are listed as such in the result.error_message,
                                                    # but do not hinder success of the response.
                                                    # Sources may include subcategories and be simulator-specific.

---

Result result
Spawnable[] spawnables                              # Spawnable objects with URI and additional information.
```
Definition of individual spawnables [Spawnable.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/Spawnable.msg):
```
# Robot or other object which can be spawned in simulation runtime.

string uri                                # URI which will be accepted by SpawnEntity service.
string description                        # Optional description for the user, e.g. "robot X with sensors A,B,C".
Bounds spawn_bounds                       # Optional spawn area bounds which fully encompass this object.
```

This service is advertising available spawnables that can be used in simulation.
We will utilize asset catalog to find those spawnables. 
There is useful API in the Engine to get products assets from asset catalog:
```cpp
AZ::Data::AssetCatalogRequests::EnumerateAssets
```
Asset catalog contains product assets (such as `.spawnables`, `.azmodel`, `.azbuffer`).
We will introduce an another product assets called `.simulationinfo`. 
This asset will be contain necessary data about Prefab that can be spawned or adjusted by Simulation Interfaces.

The `.simulationinfo` product asset will be a XML / JSON file (similar other products assets like `.physxmaterial`) and will contain at least following information:

 - a description (as a string),
 - bounds (as variant of chosen bound region),
 - list of tags,
 - category,
 - Asset Id of the corresponding prefab.


The `.simulationinfo` will be created by the asset builder that will be registered by ROS 2 Gem.
This asset builder will consume source assets called `SimulationInfo` and eventually corresponding prefabs.
It will create `SimulationInfo` product asset in the `Cache` directory.

The `SimulationInfo` source asset will be a side car (a file with the same filename) to a prefab. 
It will contain:

 - a description (as a string),
 - bounds (as variant of chosen bound region),
 - list of tags,
 - category.

A simulation expert who wants to create a robot (or other simulation ready asset) needs:
- design a new prefab using standard O3DE workflow, using all available components,
- save the prefab with a name e.g., `Robots/FooRobot.prefab`
- create and fill `SimulationInfo` file as `Robots/FooRobot.SimulationInfo` using text editor or some tooling in O3DE:
   * fill description,
   * fill tags,
   * fill category,
   * and hand-fill bounds (e.g., sphere radius),
- export simulation with export script and [bundle assets](https://docs.o3de.org/docs/user-guide/packaging/asset-bundler/)

A ROS 2 user who will call the `GetSpawnables` service against GameLauncher will trigger following sequence:
- a call to asset catalog to find all product assets of the type `.simulationinfo`.
- The `ROS 2 Entity manager` will aggregate found assets and will prepare a response that will contain:
   * `uri` as `spawnable://@cache@/robot/foorobot.spawnable`
   * the description copied from `Robots/FooRobot.SimulationInfo`,
   * the bound information copied from `Robots/FooRobot.SimulationInfo`,
   * tag list copied from `Robots/FooRobot.SimulationInfo`,
   * category copied from `Robots/FooRobot.SimulationInfo`.
- Prepared response will be returned to ROS 2 user.

## SpawnEntity service

Service allows to spawn entities found previously with `GetSpawnables` service.

Service definition [SpawnEntity](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/SpawnEntity.srv):

```
# Spawn an entity (a robot, other object) by name or URI

string name                             # A name to give to the spawned entity.
                                        # If empty, a name field in the uri file or resource_string will be used,
                                        # if supported and not empty (e.g. "name" field in SDFormat, URDF).
                                        # If the name is still empty or not unique (as determined by the simulator),
                                        # the service returns a generated name in the entity_name response field if the
                                        # allow_renaming field is set to true. Otherwise, the service call fails and an
                                        # error is returned.
bool allow_renaming                     # Determines whether the spawning succeeds with a non-unique name.
                                        # If it is set to true, the user should always check entity_name response field
                                        # and use it for any further interactions.
string uri                              # Resource such as SDFormat, URDF, USD or MJCF file, a native prefab, etc.
                                        # Valid URIs can be determined by calling GetSpawnables first, and you can check
                                        # the simulator format support by reading SimulatorFeatures spawn_formats field.
                                        # If uri field is empty, resource_string must not be empty.
string resource_string                  # An entity definition file passed as a string.
                                        # Simulators may support spawning from a file generated on the fly (e.g. XACRO).
                                        # Check whether it is supported by your simulator through GetSimulatorFeatures.
                                        # If uri field is not empty, resource_string field will be ignored.
string entity_namespace                 # Spawn the entity with all its interfaces under this namespace.
geometry_msgs/PoseStamped initial_pose  # Initial entity pose.
                                        # The header contains a reference frame, which defaults to global "world" frame.
                                        # This frame must be known to the simulator, e.g. of an object spawned earlier.
                                        # The timestamp field in the header is ignored.

---

# Additional result.result_code values for this service. Check result.error_message for further details.
uint8 NAME_NOT_UNIQUE       = 101       # Given name is already taken by entity and allow_renaming is false.
uint8 NAME_INVALID          = 102       # Given name is invalid in the simulator (e.g. does not meet naming
                                        # requirements such as allowed characters). This is also returned if name is
                                        # empty and allow_renaming is false.
uint8 UNSUPPORTED_FORMAT    = 103       # Format for uri or resource string is unsupported. Check supported formats
                                        # through GetSimulatorFeatures service, in spawn_formats field.
uint8 NO_RESOURCE           = 104       # Both uri and resource string are empty.
uint8 NAMESPACE_INVALID     = 105       # Namespace does not meet namespace naming standards.
uint8 RESOURCE_PARSE_ERROR  = 106       # Resource file or string failed to parse.
uint8 MISSING_ASSETS        = 107       # At least one of resource assets (such as meshes) was not found.
uint8 UNSUPPORTED_ASSETS    = 108       # At least one of resource assets (such as meshes) is not supported.
uint8 INVALID_POSE          = 109       # initial_pose is invalid, such as when the quaternion is invalid or position
                                        # exceeds simulator world bounds.

Result result
string entity_name                      # Spawned entity full name, guaranteed to be unique in the simulation.
                                        # If allow_renaming is true, it may differ from the request name field.
```

The ROS 2 user who want to spawn a new object in their simulation has to have a valid URI e.g.,`spawnable://@cache@/robot/foorobot.spawnable`. 

**Note !**
Spawning assets from the file system e.g., `spawnable://home/michalpelka/robots/FooRobot.spawnables` will not be supported.

With that URI `ROS 2 Entity manager` will find respective Asset Id.
Next the [SpawnableEntitiesDefinition](https://github.com/o3de/o3de/blob/152bc0a1851d881fe735adf54ec93e1ad7875b11/Code/Framework/AzFramework/AzFramework/Spawnable/SpawnableEntitiesInterface.h#L334-L333) interface will be utilized to create a spawn ticket and spawn and spawnable.

During spawning the spawnable will be modified:
- change a name of the root entity to that given by the `name` field,
- [Transform Component](https://docs.o3de.org/docs/user-guide/components/reference/transform/) of the root entity will modified to reflect value of the `initial_pose` field,
- [ROS2 Frame](https://www.docs.o3de.org/docs/user-guide/components/reference/ros2/core/ros2-frame/) to set correct namespace.
- A component called `SimulationInfoComponent` will be created and attached to root entity . 
  That component will be used to cache information in `simulationinfo` to be easily accessible in future.
- [TagComponent](https://www.docs.o3de.org/docs/user-guide/components/reference/gameplay/tag/) from LmbrCentral gem at root entity will be created (if not present in prefab).
- TagComponent will be updated with list of new tags from.


`SimulationInfoComponent` will be handling a `SimulationInfoComponentRequestBus`.
`SimulationInfoComponentRequestBus` will have an unique, string key, which will be a resulting name of spawned entity.
This bus will allow to aggregate all handling entities, and provide and API to find all spawned entities, get their state.

## GetEntities service

The service allows to discover spawned entities.

Service definition :  [GetEntities](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntities.srv )

```
# Get objects in the scene which can be interacted with, e.g. with using SetEntityState.
# You can get further information about entities through GetEntityInfo and GetEntityState services.
# There is also a GetEntitiesStates service if you would like to get state for each entity.

EntityFilters filters                               # Optional filters for the query, including name, category, tags,
                                                    # and overlap filters.

---

Result result
Entity[] entities                                   # All entities matching the filters.
```

where [Entity](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/Entity.msg) is defined:
```
# Entity identified by its unique name. Entities are objects in the simulation such as models and links.
# Each simulator might define what an entity is in a (slightly) different way.

string name                                 # Entity unique name.
```

We will serve this by calling `SimulationInfoComponentRequestBus` with `AZ::EBusAggregateResults`. 
With that, we will obtain vector of EntiesId, that can be converted to list of entities names.

## GetEntitiesStates service

This service allows users to get state (speed, location, acceleration) of chosen entities.

Service definition :  [GetEntitiesStates.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntitiesStates.srv)
```
# Get objects in the scene which can be interacted, e.g. with using SetEntityState.
# Use GetEntities service instead if EntityState information for all entities is not needed.

EntityFilters filters                               # Optional filters for the query, including name, category, tags,
                                                    # and overlap filters.

---

Result result
Entity[] entities                                   # All entities matching the filters.
EntityState[] states                                # States for these entities.
```

where [EntityState](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/EntityState.msg) is:
```
# Entity current pose, twist and acceleration

std_msgs/Header header                  # Frame and timestamp for pose and twist. Empty frame defaults to world.
geometry_msgs/Pose pose                 # Pose in reference frame, ground truth.
geometry_msgs/Twist twist               # Ground truth linear and angular velocities
                                        # observed in the frame specified by header.frame_id
                                        # See https://github.com/ros2/common_interfaces/pull/240 for conventions.
geometry_msgs/Accel acceleration        # Linear and angular acceleration ground truth, following the same convention.
```

The service handling will be similar to [GetEntities](#GetEntities). 
There will be more calls to :
 - [RigidBodyRequestBus](https://github.com/o3de/o3de/blob/79e1df3990c5554c454d68798a0f3ef94c8ae317/Code/Framework/AzFramework/AzFramework/Physics/RigidBodyBus.h) to find current twist,
 - [TransformComponentRequests](https://github.com/o3de/o3de/blob/42d375dd99b972400f9f26bfec7e3444088f3398/Code/Framework/AzCore/AzCore/Component/TransformBus.h) to find current pose,
 - [ROS2FrameBus.h](https://github.com/o3de/o3de-extras/blob/development/Gems/ROS2/Code/Include/ROS2/Frame/ROS2FrameBus.h) to find frame id.

**Note !** acceleration is will not be filled, and will not be supported.

#### SetEntityState service

This service allows to modify state of chosen entity.

Service definition [SetEntityState.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/SetEntityState.srv)
```
# Set a state of an object, which will result in an instant change in its pose and/or twist.

Entity entity                           # Entity identified by its unique name as returned by GetEntities / SpawnEntity.
EntityState state                       # New state to set immediately. The timestamp in header is ignored.
                                        # Note that the acceleration field may be ignored by simulators.

---

Result `result`:
```
where [EntityState](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/EntityState.msg) is:
```
# Entity current pose, twist and acceleration

std_msgs/Header header                  # Frame and timestamp for pose and twist. Empty frame defaults to world.
geometry_msgs/Pose pose                 # Pose in reference frame, ground truth.
geometry_msgs/Twist twist               # Ground truth linear and angular velocities
                                        # observed in the frame specified by header.frame_id
                                        # See https://github.com/ros2/common_interfaces/pull/240 for conventions.
geometry_msgs/Accel acceleration        # Linear and angular acceleration ground truth, following the same convention.
```

The O3DE entity Id will be discovered by calling `SimulationInfoComponentRequestBus` with entity name provided in service request.
Next, having the EntityId the corresponding API will be called to adjust state:
 - [TransformComponentRequests](https://github.com/o3de/o3de/blob/42d375dd99b972400f9f26bfec7e3444088f3398/Code/Framework/AzCore/AzCore/Component/TransformBus.h) or 
   [RigidBodyRequestBus](https://github.com/o3de/o3de/blob/79e1df3990c5554c454d68798a0f3ef94c8ae317/Code/Framework/AzFramework/AzFramework/Physics/RigidBodyBus.h) to 
   adjust position of simulated entity. 
 - [ROS2FrameBus.h](https://github.com/o3de/o3de-extras/blob/development/Gems/ROS2/Code/Include/ROS2/Frame/ROS2FrameBus.h) to eventually change frame id,
 - [RigidBodyRequestBus](https://github.com/o3de/o3de/blob/79e1df3990c5554c454d68798a0f3ef94c8ae317/Code/Framework/AzFramework/AzFramework/Physics/RigidBodyBus.h) to 
   adjust speed or acceleration.

Note that serving this service is a bit tricky in O3DE.
Due to modularity of O3DE the entities can be moved around in several ways:
- Simulated PhysX Rigid Body,
- Kinematic PhysX Rigid Body,
- TransformCompetent (due to parent-child relation),
- TransformComponent (due to calls to EBuses),
- PhysX character component,
- PhysX ragdoll,
- PhysX articulations,
- Potential 3rd physics engine.

Some of those methods respects calls to `TranformComponentRequests`, but some of them needs to call directly methods in PhysX gem.
| Method                                 |  TransformComponentRequests API| RigidBodyRequestBus API  | Custom API |
|----------------------------------------|--------------------------------|--------------------------|------------|
|Simulated PhysX Rigid Body              |                                | supports                 |            |
|Kinematic PhysX Rigid Body              |  supports                      |                          |            |
|TransformComponent  due to parent-child |  supports                      |                          |            |
|TransformComponent (custom calls)       |                                |                          |            |
|PhysX character component               |                                |                          |      ?     |
|PhysX ragdoll                           |                                |                          |      ?     |
|PhysX articulations                     |                                |                          | supports   |

The correct API need to be chosen to get expected outcome that it can be quite tricky.
Let us investigate moving a `Simulated PhysX Rigid Body'.
We cannot simply call :
```cpp
 AZ::TransformBus::Event(entityId, &AZ::TransformBus::Events::SetWorldTM, transform);
```
Above snippet will work fine in setup where the entity has PhysX rigid body component set to kinematic.
When  PhysX rigid body component is set to simulated type, it will have no effect.
Proper way to move such entity (respecting joints and contacts) will be:
- call `SetKinematic(true)`  from RigidBodyRequestBus to stop simulation,
- wait for SetKinematic to be effective,
- set `SetKinematicTarget` and wait for target reached,
- set `SetKinematic(false)` and wait for effect,
- finally, call `ForceAwake` to make sure that PhysX will run simulation of the body.

It can take multiple cycles to achieve this.

This can lead to few problems:
 - ROS2 Gem need to be heavily reliable on PhysX5 gem to support moving entities with articulations, characters.
 - Some of methods to adjust pose can be quite challenging.
 - Introducing new physics engine can lead to conflicts here.

Alternative, approach would be to cycle through:
- disable physics simulation of [simulated body](https://docs.o3de.org/docs/user-guide/interactivity/physics/nvidia-physx/simulated-bodies/),
- Use `TransformBus::Events::SetWorldTM` to change location,
- enable simulation of [simulated body](https://docs.o3de.org/docs/user-guide/interactivity/physics/nvidia-physx/simulated-bodies/).

The velocity (twist) can be applied using `RigidBodyRequestBus::SetLinearVelocity` and  `RigidBodyRequestBus::SetAngularVelocity`.
It will be effective against Simulated PhysX Rigid Body and Kinematic PhysX Rigid Body and should give a warning if it called against 
unsupported type.


The acceleration can be applied using `RigidBodyRequestBus::ApplyLinearImpulse` w.r.t object's mass.
It will be effective against Simulated PhysX Rigid Body and should give a warning if it called against 
unsupported type.

**Important :** Currently ROS2 Gem is heavily dependant on PhysX5 (due to SDF importer). The decoupling of those need to be done in future.

## DeleteEntity service
 
Despawn previously spawned entity

Service definition [DeleteEntity.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/DeleteEntity.srv)
```
# Remove an entity (a robot, other object) from the simulation

Entity entity                         # Entity identified by its unique name with a namespace,
                                      # as returned by SpawnEntity or GetEntities.

---

Result result
```

The O3DE entity ID and their spawn ticket ID will be discovered by `SimulationInfoComponentRequestBus`.
Next the component `ROS 2 Entity manager` will be asked to despawn and remove corresponding spawn ticket.

**Important :** This mechanism will now allow to delete entities that are part of the level prefab (e.g., prefab instantiated in Editor).


## GetEntityBounds service

Returns bounds of spawned entity.

Service definition [GetEntityBounds.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntityBounds.srv)
```
# Get geometrical bounds for entity. This feature is available if GetSimulatorFeatures includes ENTITY_BOUNDS feature.

Entity entity                         # Entity identified by its unique name as returned by GetEntities / SpawnEntity.

---

Result result
Bounds bounds  
```

The bounds for particular entities will be get by calling `SimulationInfoComponentRequestBus`.


## GetEntityInfo service

Returns tags and category of spawned entity.

Service definition [GetEntityInfo.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetEntityInfo.srv):
```
# Get type and other information about an entity.

Entity entity                         # Entity identified by its unique name as returned by GetEntities / SpawnEntity.

---

Result result
EntityInfo info                       # Only valid if result.result_code is OK.
```
where [EntityInfo.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/EntityInfo.msg):
```
# Entity type and additional information

uint8 category              # Major category for the entity. Extra entity type distinction can be made through tags.
                            # See EntityCategories for defined category values.
string description          # optional: verbose, human-readable description of the entity.
string[] tags               # optional: tags which are useful for filtering and categorizing entities further.
```

Those information will be obtained from `SimulationInfoComponentRequestBus`.

## GetNamedPoses service

Simulation expert using O3DE Editor can add entity with component from ROS2 gem called `Named Pose Editor Component`.
This component will require `TransformService` and dependent service `BoxShapeService` and `SphereShapeService`
The  `Named Pose Component` will contain two configurable field called `description` and `tags`.
During creation of game component (`CreateGameEntity`) the TagComponent from LmbrCentral gem will be created and fed with list of tags. 
Creating the TagComponent is useful to make this feature compatible with O3DE's tag system.
The box or sphere shape component allows to define optional boundaries around named pose.

**Important** Convex hull bound shape will not be supported yet.

Calling service `GetNamedPose` will discover those components and returns theirs configuration.

Service definition [GetNamedPoses.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetNamedPoses.srv):
```
# Get predefined poses which are convenient to for spawning, navigation goals etc.

TagsFilter tags                                     # Tags filter to apply. Only named poses with matching tags field
                                                    # will be returned. Can be empty (see TagsFilter).

---

Result result
NamedPose[] poses                                   # A list of predefined poses, which may be empty.
```
where: [TagsFilter.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/TagsFilter.msg),
[NamedPose.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/NamedPose.msg):
```
# A named pose defined in the simulation for certain purposes such as spawning.

string name                               # Unique name.
string description                        # Description for the user, e.g. "near the charging station".
string[] tags                             # Optional tags which can be used to determine the named pose
                                          # purpose, for example: "spawn", "parking", "navigation_goal",
                                          # as well as fitting entity types e.g. "drone", "turtlebot3".
geometry_msgs/Pose pose                   # Pose relative to world, which can be used with SpawnEntity.srv.
```

During simulation entities with `Named Pose Component` will be discovered (e.g., using designated request bus or o3de tag system),
the aggregation will be filtered and returned to caller. 

**Important** Since O3DE does not requires those names to be unique, the ROS 2 gem needs to skip or extend names of duplicates.


## GetNamedPoseBounds service

Service allows to get boundaries defined in Named Pose.

Service definition [GetNamedPoseBounds.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetNamedPoseBounds.srv):
```
# Get bounds for the named pose. This feature is available if GetSimulatorFeatures includes POSE_BOUNDS feature.

string name                                         # unique names (as returned from GetNamedPoses).

---

Result result
Bounds bounds                                       # bounds for the named pose.
```

It will expose data in Named Pose Game Component and corresponding Shape Components.

## Reset Simulation service

Allows ROS 2 user to reset simulation.

Service definition [ResetSimulation.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/ResetSimulation.srv)
```
# Reset the simulation to the start, including the entire scene and the simulation time.
# Objects that were dynamically spawned are de-spawned.

uint8 SCOPE_DEFAULT       = 0             # same as ALL.
uint8 SCOPE_TIME          = 1             # Reset simulation time to start.
uint8 SCOPE_STATE         = 2             # Reset state such as poses and velocities. This may include state randomization
                                          # if such feature is available and turned on.
uint8 SCOPE_SPAWNED       = 4             # De-spawns all spawned entities.
uint8 SCOPE_ALL           = 255           # Fully resets simulation to the start, as if it was closed and launched again.

uint8 scope                               # Scope of the reset. Note that simulators might only support some scopes.
                                          # This is a bit field which may be checked for each scope e.g. scope & TIME.

---

Result result
```

This service will be need to use multiple APIs to give results.

|Scope           | Planned API and usage |
|----------------|--------------------
|SCOPE_ALL       | ConsoleRequestBus and `LoadLevel` command.
|SCOPE_SPAWNED   | Internal gem API to destroy all spawn tickets in ROS 2 Entities manager.
|SCOPE_STATE     | Move all spawned entities to intial poses cached in ROS 2 Entities manager.
|SCOPE_TIME      | New ROS2Bus call

## SetSimulationState service

Allows ROS 2 user to set state of the simulation (STOPPED, PAUSED, PLAYING, QUITTING)

```
# Change the simulation state


SimulationState state                      # Target state to set for the simulation.

---

uint8 ALREADY_IN_TARGET_STATE   = 101      # Additional result type for this call, which means simulation was already
                                           # in the target state (e.g. was already stopped when STOP was requested).
uint8 STATE_TRANSITION_ERROR    = 102      # The simulator failed to transition to the target state. This might happen
                                           # especially with the transition to PLAYING from STOPPED.
                                           # See result.error_message for details.
uint8 INCORRECT_TRANSITION      = 103      # Incorrect transition (pausing when in stopped state).

Result result

```
where [SimulationState](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/SimulationState.msg) is defined as:
```
# Simulation states used in SetSimulationState and returned in GetSimulationState

uint8 STOPPED      = 0                 # Simulation is stopped, which is equivalent to pausing and resetting with ALL.
                                       # This is typically the default state when simulator is launched.
                                       # Stopped simulation can be played. It can also be paused, which means
                                       # starting simulation in a paused state immediately,
                                       # without any time steps for physics or simulated clock ticks.
uint8 PLAYING      = 1                 # Simulation is playing, can be either paused or stopped.
uint8 PAUSED       = 2                 # Simulation is paused, can be either stopped (which will reset it) or played.
uint8 QUITTING     = 3                 # Closing the simulator application. Switching from PLAYING or PAUSED states
                                       # is expected to stop the simulation first, and then exit.
                                       # Simulation interfaces will become unavailable after quitting.
                                       # Running simulation application is outside of the simulation interfaces as
                                       # there is no service to handle the call when the simulator is not up.

uint8 state
```

Transition from PLAYING to STOPPED will trigger level reloading. That will take a while to complete. \
Transition from PLAYING to PAUSED will ask default physics scene to be disabled. 
It will stop movement of all PhysX articulations, rigid bodies (both kinematic and simulated) and characters, but some animations will be played.
Transition from PLAYING to QUITTING will simply calling :
```cpp
 int* ptr = nullptr;
 *ptr= 99;
```
alternatively:
```cpp
#pragma clang diagnostic ignored "-Winfinite-recursion"
auto exit = [&]() { exit(); }; exit();
```

The ROS 2 Simulator manager will contain state of the simulation and perform necessary transitions.

## GetSimulationState service

Allows ROS 2 user to get current state of the simulation.

Service definition [GetSimulationState.srv](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/GetSimulationState.srv)

```
# Gets the simulation state (paused, playing, stopped)

---

SimulationState state                      # Current state of the simulation.

Result result
```
The ROS 2 Simulator manager will return current state.
If transition is in progress (e.g. reloading level or despawning), the old state will be returned.



## Retired components in ROS 2 Gem

During this effort some components will be retired, and some moved outside of scope of supported and canonical repos.

### ROS2SpawnerComponent

ROS2Spawner component will be retired and hidden away from users with CMake variable.
This component can co-exists with managers for Simulation Interfaces, but ROS 2 package that components requires will be removed from upcoming ROS 2 distribution (Kilted Kaiju).
The said package (`gazebo_msgs`) will be replaced by simulation_interfaces.
Component will be retired (instead of complete removal from codebase) to make transition to Simulation interfaces easier experience.
However using ROS2Spawner component will require few extra steps (after EOL of gazebo_msgs package):
- cloning and building the latest [gazebo_ros_pkgs/gazebo_msgs](https://github.com/ros-simulation/gazebo_ros_pkgs/tree/3.9.0/gazebo_msgs)
- sourcing custom workspace
- setting CMake flag
- building ROS2 gem from source.

### ROS2ContactSensorComponent

This component's code also depends on [`gazebo_msgs`](https://github.com/ros-simulation/gazebo_ros_pkgs/blob/3.9.0/gazebo_msgs/msg/ContactState.msg).
Since there is no equivalent message in simulation_interfaces component will be retired and hidden from Users with CMakeVariable.
The steps to use this component will be the same to those in ROS2SpawnerComponent.

Note that ROS 2 community probably propose suitable replacement message in future.


<!-- - Explain the technical portion of the work in enough detail that members can implement the feature. 

- Explain any API or process changes required to implement this feature

- This section should relate to the feature design description by reference and explain in more detail how it makes the feature design examples work.

- This should also provide detailed information on compatibility with different hardware platforms. -->


### What are the advantages of the feature?
<!-- - Explain the advantages for someone to use this feature -->
 - Leading of standardization in robotics domain,
 - easier usage of O3DE in robotics and ML pipelines,
 - improve testability of the ROS 2 features,
 - adjustments to EOL of `gazebo_msgs`

### What are the disadvantages of the feature?
<!-- - Explain any disadvantages for someone to use this feature -->
Significant increase in code base size 

### How will this be implemented or integrated into the O3DE environment?
<!-- - Explain how this will be integrated within codebase of O3DE and any special library or technical stack requirements. -->
It was explained in great details in section [ROS 2 API](#ros-2-api)

### Are there any alternatives to this feature?
<!-- - Provide any other designs that have been considered. Explain what the impact might be to not doing this.
- If there is any prior art or approaches with other frameworks in the same domain, explain how they may have solved this problem or implemented this feature. -->
Really, the effort needs to be taken to adjust code to deprecation of gazebo classic and `gazebo_msgs`.


### How will users learn this feature?
<!-- - Detail how it can be best presented and how it is used as an extension or as a standalone tool used with O3DE.
- Explain if and how it may change the approach of how individuals would use the platform and if any documentation must be changed or reorganized.
- Explain how it would be taught to new and existing O3DE users.
- Include any significant impacts to documentation such as; Required official video updates, required product screenshot updates (i.e. Editor UX changes), new documentation site sections.  -->

- Posts by OpenRobotics in their social media
- documentation in [o3de.org](o3de.org) and [docs.ros.org](https://docs.ros.org/en/jazzy/Tutorials/Advanced/Simulators/Simulation-Main.html) 

### Are there any open questions?

- Handling integration of physics engine.
- Timing of implementation and release.