# Introduce Simulation Interfaces to ROS 2 Gem 
<!-- This RFC template should be used for any feature that is not a bug or a substantial reorganization of the O3DE product.

If you submit a pull request to implement a new feature without going through the RFC process, it may be closed with a polite request to submit an RFC first.

A hastily-proposed RFC can hurt its chances of acceptance. Low quality proposals, proposals for previously-rejected features, or those that don't fit into the near-term roadmap, may be quickly rejected, which can be demotivating for the unprepared contributor. Laying some groundwork ahead of the RFC can make the process smoother.

Although there is no single way to prepare for submitting an RFC, it is generally a good idea to pursue feedback from other project developers beforehand, to ascertain that the RFC may be desirable; having a consistent impact on the project requires concerted effort toward consensus-building.

The most common preparations for writing and submitting an RFC include talking the idea over on our Discord server, discussing the topic on our GitHub RFCs discussions page, and occasionally posting "pre-RFCs" on the GitHub RFCs discussion page. You may file issues in the RFCs repo for discussion, but these are not actively looked at by the teams.

As a rule of thumb, receiving encouraging feedback from long-standing project developers, and particularly members of the relevant sub-team is a good indication that the RFC is worth pursuing. -->

### Summary:
<!-- Single paragraph explanation of the feature -->

This RFC explains in detail the idea of splitting the existing _ROS2_ Gem into a set of Gems and describes the reasoning behind the architecture. The implementation will not add features to the simulation-related packages, but only refactor the existing code into separate packages to fully exploit the modular architecture of the O3DE engine. This change will allow to substitute some parts of the simulation packages with different ones in the future, e.g. decoupling sensors from the _ROS 2_ ecosystem will allow using different frameworks with the existing O3DE implementation. Besides, the proposed changes will increase the testability of the simulation-related packages. The changes will be limited to the _ROS2_ Gem, hence all changes will be implemented in `o3de-extras` repository.

### What is the relevance of this feature?
<!-- Why is this important? What are the use cases? What will it do once completed? -->

_ROS2_ Gem implements a number of functionalities:
- _ROS 2 Node_ (singleton) allowing the communication with the _ROS 2_ ecosystem
- automated handling of simulation time
- handling of transformation frames
- handling ROS 2 _namespaces_ and ROS 2 _topics_
- dynamic objects spawning via ROS 2 _services_
- sample sensors (camera, contact, GNSS, imu, lidar, odometry)
- robot control components
- vehicle dynamics
- manipulation and grippers controllers
- robot importer tool

While it is convenient for the users to add one Gem to their simulation projects and get all features, it does not follow the modular architecture of O3DE. Not every user needs all listed features. The biggest disadvantages of the current solution is lack of possibilities to reuse the code and to substitute parts of the implementations with different ones. In particular, it forces users to stick to ROS 2 ecosystem and _PhysX5_ physics engine. Moreover, it requires _SDFormat_ libraries for the importer, which might be a hassle when trying to build the simulation on Windows platform.

### Feature design description:
<!-- - Explain the design of the feature with enough detail that someone familiar with the environment and framework can understand the concept and be able to explain it to others. 
- It should include at least one end to end example of how it will be used and specific details with outlying use cases. 

- If there is any new terminology, it should be defined here. -->

#### List of created Gems

Three Gems will be separated from the existing codebase of _ROS2_ Gem:

| Gem name            | Description                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------ |
| _ROS2_              | Base Gem for any _ROS 2_ based Gem/project, including:                                     |
|                     | - _ROS 2 Node_ (singleton) allowing the communication with the _ROS 2_ ecosystem           |
|                     | - automated handling of simulation time                                                    |
|                     | - handling of transformation frames                                                        |
|                     | - handling ROS 2 _namespaces_ and ROS 2 _topics_                                           |
|                     | - dynamic objects spawning via ROS 2 _services_                                            |
| _ROS2Sensors_       | Gem interfacing with _ROS 2_ ecosystem implementing the following simulation sensors:      |
|                     | camera, contact, GNSS, imu, lidar, odometry                                                |
| _ROS2Controllers_   | Gem interfacing with _ROS 2_ ecosystem implementing robot control, manipulation, grippers, |
|                     | and vehicle dynamics                                                                       |
| _ROS2RobotImporter_ | Gem implementing robot importer tool using _ROS 2_ components                              |

> Note: the _contact_ sensor (within _ROS2Sensors_ Gem) and the _spawner_ component (_ROS2_ Gem) will be marked deprecated, as explained in [RFC : Simulation interfaces](https://github.com/o3de/sig-simulation/pull/95).

Due to the changed API of _ROS2_ Gem (the same name is already present), its version will be changed to `4.0.0`. The further split of _ROS2Sensors_ and _ROS2Controllers_ Gems to separate the robotic communication interface (_ROS 2_) from the O3DE implementation and allow changing the communication interface will follow in a separate RFC when necessary. The newly created Gems will keep the same _namespace_, namely `ROS2`, for backward compatibility.

#### List of dependencies for created Gems

The list of the dependencies after the changes (other than _core_ O3DE Gems) is the following:

| Gem name            | O3DE dependencies                                  | External dependencies |
| ------------------- | -------------------------------------------------- | --------------------- |
| _ROS2_              | -                                                  | _ROS 2_ ecosystem     |
| _ROS2Sensors_       | _ROS2_, _PhysX5_                                   | -                     |
| _ROS2Controllers_   | _ROS2_, _PhysX5_                                   | -                     |
| _ROS2RobotImporter_ | _ROS2_, _PhysX5_, _ROS2Sensors_, _ROS2Controllers_ | _SDFormat_ library    |

_ROS2Sensors_ Gem will require _PhysX5_ due to the current implementation of _imu_ sensors. _ROS2Controllers_ relies on the game engine due to multiple reasons. Decoupling _ROS2Sensors_ and _ROS2Controllers_ Gems from _PhysX5_ dependency will be implemented independently of this RFC. The change will require an additional API within the engine. The same changes are required for _ROS2RobotImporter_ Gem, which utilizes _PhysX5_ API to create joints and/or articulation links.

#### Proposed communication

The current implementation of _ROS2_ Gem allows for the communication using O3DE buses. In particular, `ROS2Bus` shares the _ROS 2 node_ for creating publishers and subscribers, gives the access to the clock, etc. Additionally, `ROS2Names` interface and `ROS2Conversions` support handling of the _namespaces_, _topics_ and let users convert values between O3DE types and _ROS 2_ types. Finally, `ROS2FrameBus` and `ROS2Transform` support transformation frames. All listed buses, as well as the currently available public interfaces related to the _ROS 2_ ecosystem, will exist in the updated Gem.

_ROS2Sensors_ and _ROS2Controllers_ Gems will require _ROS2_ Gem dependency to work correctly. The communication between the two and _ROS2_ Gem will be handled using busses listed earlier. This is already the case in the current implementation.

The communication between _ROS2RobotImporter_ Gem and dependencies requires significant changes. The current implementation is based on _hooks_, that can be created in any Gem based on the public declaration. Such _hook_ is then defined in the same or another Gem and registered as an _attribute_ in the _serialized context_. The registration makes it available to the robot importer. Each _hook_ is responsible for translating parameters from one format, e.g. _SDF_, into O3DE components. Hence, such _hook_ needs to link with both interfaces. Placing _hooks'_ implementations in _ROS2Sensors_ (and _ROS2Controllers_) would make them dependent on _ROS2RobotImporter_. Therefore, all _hooks_ will be an integral part of the _ROS2RobotImporter_. This will make it dependent on _ROS2Sensors_ and _ROS2Controllers_. Moreover, the public interface of all implemented sensors will require an update, as the current one does not allow modifying certain parameters. This means, all sensor's parameters will become a part of the API, similarly to the base configuration stored in `SensorConfiguration` structure.

#### Proposed changes in the API

The following structures will be available as a part of the public API:

| Gem name          | Configuration                    | Description                                                 |
| ----------------- | -------------------------------- | ----------------------------------------------------------- |
| _ROS2Sensors_     | `CameraSensorConfiguration`      | The configuration of `ROS2CameraSensorComponent`:           |
|                   |                                  | the current implementation will be made public.             |
| _ROS2Sensors_     | `ImuSensorConfiguration`         | The configuration of `ROS2ImuSensorComponent`:              |
|                   |                                  | the current implementation will be made public.             |
| _ROS2Sensors_     | `LidarSensorConfiguration`       | The configuration of `ROS2LidarSensorComponent`             |
|                   |                                  | and `ROS2Lidar2DSensorComponent`:                           |
|                   |                                  | the current implementation will be made public.             |
| _ROS2Sensors_     | `SegmentationClassConfiguration` | The configuration used by `ROS2LidarSensorComponent`:       |
|                   |                                  | the current implementation will be made public.             |
| _ROS2Controllers_ | `AckermannModelLimits`           | The configuration used by `AckermannVehicleModelComponent`: |
|                   |                                  | the current implementation will be made public.             |
| _ROS2Controllers_ | `SkidSteeringModelLimits`        | The configuration used by `SkidSteeringModelComponent`:     |
|                   |                                  | the current implementation will be made public.             |
| _ROS2Controllers_ | `VehicleConfiguration`           | The configuration used by `AckermannVehicleModelComponent`  |
|                   |                                  | and `SkidSteeringModelComponent`:                           |
|                   |                                  | the current implementation will be made public.             |
| _ROS2Controllers_ | `AxleConfiguration`              | The configuration used by `VehicleConfiguration`:           |
|                   |                                  | the current implementation will be made public.             |

Additionally, an API to create components that can be loaded by the importer will be created in _ROS2Sensors_ and _ROS2Controllers_.

#### Implementation details

The implementation of this refactor will take place on a separate branch on `o3de-extras` repository. The following steps will take place:
1. Creating new empty Gems
2. Disabling sensor and plugin _hooks_ from _Robot Importer_ tool in _ROS2_ Gem
3. Moving the content of _ROS2Sensors_ from _ROS2_ Gem to the new Gem
4. Moving the content of _ROS2Controllers_ from _ROS2_ Gem to the new Gem
5. Moving the _Robot Importer_ tool from _ROS2_ Gem to the new Gem
6. Implementing new API in _ROS2Sensors_ and _ROS2Controllers_
7. Enabling back sensor and plugin _hooks_ in _ROS2RobotImporter_ Gem

# Conclusions

<!-- - Explain the technical portion of the work in enough detail that members can implement the feature. 

- Explain any API or process changes required to implement this feature

- This section should relate to the feature design description by reference and explain in more detail how it makes the feature design examples work.

- This should also provide detailed information on compatibility with different hardware platforms. -->

## What are the advantages of the feature?
<!-- - Explain the advantages for someone to use this feature -->

- Decoupling some simulation modules from _PhysX_ Gem, _ROS 2_ dependencies, or _SDFormat_ dependencies
- Opening possibilities for the future implementations (different physics engines; different simulation frameworks)
- Improving testability of the simulation components

## What are the disadvantages of the feature?
<!-- - Explain any disadvantages for someone to use this feature -->

There are two disadvantages of the proposed refactor.
- Increase of the number of Gems required to build a simulation.
- The lack of backward compatibility for projects using _ROS2_ Gem: multiple Gems will have to be added to the projects when migrating to the newest version of simulation Gems.

### How will this be implemented or integrated into the O3DE environment?
<!-- - Explain how this will be integrated within codebase of O3DE and any special library or technical stack requirements. -->
The proposed refactor will make use of the existing O3DE mechanisms, such as _gems_ to make the simulation structure modular, _busses_ for the communication and _serialize context_ for feature registration. No changes in O3DE environment are required for the proposed implementation. I.e., this RFC is limited to `o3de-extras` repository.

Additional changes will be required for the follow-ups only.

### Are there any alternatives to this feature?
<!-- - Provide any other designs that have been considered. Explain what the impact might be to not doing this.
- If there is any prior art or approaches with other frameworks in the same domain, explain how they may have solved this problem or implemented this feature. -->

It is difficult to point out any alternative for the modularity other than the lack of modularity. There are infinite ways to split the current codebase into modules though and to organize communication between them. I am open for comments on the chosen architecture.

### How will users learn this feature?
<!-- - Detail how it can be best presented and how it is used as an extension or as a standalone tool used with O3DE.
- Explain if and how it may change the approach of how individuals would use the platform and if any documentation must be changed or reorganized.
- Explain how it would be taught to new and existing O3DE users.
- Include any significant impacts to documentation such as; Required official video updates, required product screenshot updates (i.e. Editor UX changes), new documentation site sections.  -->

- Documentation in [o3de.org](o3de.org) and [docs.ros.org](https://docs.ros.org/en/jazzy/Tutorials/Advanced/Simulators/Simulation-Main.html).
- Discord social platform of O3DE.
- Posts by OpenRobotics and Robotec.ai in their social media.
- New robotic templates.

### Are there any open questions?

The main problem is the proper API to create components (e.g. `JointsArticulationControllerComponent`, `ROS2GNSSSensorComponent` or `ROS2CameraSensorEditorComponent`) from the _ROS2RobotImporter_ Gem.
