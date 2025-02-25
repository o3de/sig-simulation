# Introduce Simulation Interfaces to ROS 2 Gem 
<!-- This RFC template should be used for any feature that is not a bug or a substantial reorganization of the O3DE product.

If you submit a pull request to implement a new feature without going through the RFC process, it may be closed with a polite request to submit an RFC first.

A hastily-proposed RFC can hurt its chances of acceptance. Low quality proposals, proposals for previously-rejected features, or those that don't fit into the near-term roadmap, may be quickly rejected, which can be demotivating for the unprepared contributor. Laying some groundwork ahead of the RFC can make the process smoother.

Although there is no single way to prepare for submitting an RFC, it is generally a good idea to pursue feedback from other project developers beforehand, to ascertain that the RFC may be desirable; having a consistent impact on the project requires concerted effort toward consensus-building.

The most common preparations for writing and submitting an RFC include talking the idea over on our Discord server, discussing the topic on our GitHub RFCs discussions page, and occasionally posting "pre-RFCs" on the GitHub RFCs discussion page. You may file issues in the RFCs repo for discussion, but these are not actively looked at by the teams.

As a rule of thumb, receiving encouraging feedback from long-standing project developers, and particularly members of the relevant sub-team is a good indication that the RFC is worth pursuing. -->

### Summary:
<!-- Single paragraph explanation of the feature -->

There is an effort at [ros-simulation/simulation_interfaces](https://github.com/ros-simulation/simulation_interfaces/pull/1) to standarized simulation interfaces existing robotics simulators.


### What is the relevance of this feature?
<!-- Why is this important? What are the use cases? What will it do once completed? -->

Citing [RFC](https://github.com/ros-infrastructure/rep/issues/410) in ros-infrastructure/rep:
```
Standardization would improve user experience when using their validation, testing and ML pipelines with several simulators, or when switching between one simulator and the other as they needs come to better match another platform. It would also make it easier to benchmark simulators.
```

### Feature design description:
<!-- - Explain the design of the feature with enough detail that someone familiar with the environment and framework can understand the concept and be able to explain it to others. 
- It should include at least one end to end example of how it will be used and specific details with outlying use cases. 

- If there is any new terminology, it should be defined here. -->

The ROS 2 simulator that is compatible with simulation interfaces has a number of requirements to follow.

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

 - SIMULATION_PAUSE

We do not plan to support the moment:
 - SIMULATION_RESET_STATE
 - STEP_SIMULATION_SINGLE
 - STEP_SIMULATION_MULTIPLE
 - STEP_SIMULATION_ACTION

Following formats will be supported for spawnaning (field `spawn_formats` of [SimulatorFeatures.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/SimulatorFeatures.msg)):
```
[`.spawnables`]
```

**Note** other formats e.g. `URDF` and `SDF` are supported by ROS 2 Gem but only in Editor. 
Those tools are not available in the game mode, so spawning `SDF` and `URDF` would require:
 - Handling mesh importing in Game Launcher and preparing it to use with Mesh Feature processor
 - Creating materials and texture assets in runtime
 - PhysX (or other Physics engine) collider mesh cooking with decomposition.

Such feature, would be usefull, but it is out of scope of this RFC.

### Technical design description:

Spawning, gathering information and despawning will be carried by 
`ROS 2 Entity manager`.
This manager will be a system component that will be part of ROS 2 Gem.
It will advertise following services:

#### GetSpawnables
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
Definition of individual spawnable [Spawnable.msg](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/msg/Spawnable.msg)
```
# Robot or other object which can be spawned in simulation runtime.

string uri                                # URI which will be accepted by SpawnEntity service.
string description                        # Optional description for the user, e.g. "robot X with sensors A,B,C".
Bounds spawn_bounds                       # Optional spawn area bounds which fully encompass this object.
```

This service is advertising available spawnables that can be used in simulation.
We will take asset catalog to find spawnables. 
There is usefull API in the Engine to get products assets:
```cpp
 AZ::Data::AssetCatalogRequestBus::Broadcast(&AZ::Data::AssetCatalogRequests::EnumerateAssets, nullptr, enumerateCallback, nullptr);
```
Asset catalog contains product assets (such as spawnables, azmodel, azbuffer).
We will introduce a product assets called `SimulationInfo`.



#### GetEntitiesStates
Service definition :  [GetEntitiesStates](https://github.com/adamdbrw/simulation_interfaces/blob/simulation_interfaces/srv/SetEntityState.srv)
- 

<!-- - Explain the technical portion of the work in enough detail that members can implement the feature. 

- Explain any API or process changes required to implement this feature

- This section should relate to the feature design description by reference and explain in more detail how it makes the feature design examples work.

- This should also provide detailed information on compatibility with different hardware platforms. -->


### What are the advantages of the feature?
<!-- - Explain the advantages for someone to use this feature -->

### What are the disadvantages of the feature?
<!-- - Explain any disadvantages for someone to use this feature -->

### How will this be implemented or integrated into the O3DE environment?
<!-- - Explain how this will be integrated within codebase of O3DE and any special library or technical stack requirements. -->

### Are there any alternatives to this feature?
<!-- - Provide any other designs that have been considered. Explain what the impact might be to not doing this.
- If there is any prior art or approaches with other frameworks in the same domain, explain how they may have solved this problem or implemented this feature. -->

### How will users learn this feature?
<!-- - Detail how it can be best presented and how it is used as an extension or as a standalone tool used with O3DE.
- Explain if and how it may change the approach of how individuals would use the platform and if any documentation must be changed or reorganized.
- Explain how it would be taught to new and existing O3DE users.
- Include any significant impacts to documentation such as; Required official video updates, required product screenshot updates (i.e. Editor UX changes), new documentation site sections.  -->

### Are there any open questions?
<!-- - What are some of the open questions and potential scenarios that should be considered? -->
