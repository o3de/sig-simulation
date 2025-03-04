# RFC Feature - Cleaning up Gems and Templates

This RFC lists a set of proposed changes in simulation-related Gems and project Templates hosted within the [o3de-extras](https://github.com/o3de/o3de-extras) repository. The goal is to discuss and to justify the needed modifications. It is triggered by the issues with the maintenance and testing of the large amount of assets and codebase with each release.

The RFS briefly describes every simulation-related component and lists possible changes. It is important to note that the past releases of the Gems will be stored unchanged.

## LevelGeoreferencing Gem

#### Summary
This Gem enables advanced robotics cases, simulation, Earth science, geodesy, cartography, and surveying. It exposes API that simplifies integration with formats like [KML](https://pl.wikipedia.org/wiki/Keyhole_Markup_Language) or [GeoJSON](https://pl.wikipedia.org/wiki/GeoJSON).

#### Proposed changes
No changes.

## ProteusRobot Gem

#### Summary
This Gem contains assets (meshes, textures and ready-to-use *prefabs*) of `Proteus` robot by *Amazon Robotics*.

#### Proposed changes
There are two gems with sample robots: `ProteusRobot` and `RosRobotSample`. Additionally, *Panda Franka* robot is included within `Ros2RoboticManipulationTemplate` project Template. We propose to create a new Gem, `RosRobotSamples`, with three sample robots: `Proteus`, `Panda Franka`, and `Husarion ROSBot XL`.

#### Consequences for users
All projects using `ProteusRobot` Gem will require an easy update: changing the required Gem name. All asset names and paths will remain unchanged.

## ROS2 Gem

#### Summary
This Gem is a base for any robotic simulation using O3DE; it implements a ROS 2 node to enable ROS 2 communication and implements O3DE sensor components, such as camera, IMU, GNSS, Lidar, etc. Additionally, it supports the robot control mechanisms (with Ackerman, skid steering, and rigid body models) and joints' manipulation. Finally, it enables importing URDF/SDF robots.

#### Proposed changes
The changes within this Gem will be covered by separate RFCs due to the size of this Gem and the amount of required changes.

## RosRobotSample Gem

#### Summary
This Gem contains assets (meshes, textures and ready-to-use *prefabs*) of `ROSBot XL` robot by *Husarion*.

#### Proposed changes
There are two gems with sample robots: `ProteusRobot` and `RosRobotSample`. Additionally, the *Panda Franka* robot is included in the `Ros2RoboticManipulationTemplate` project template. We propose to create a new Gem, `RosRobotSamples`, with three sample robots: `Proteus`, `Panda Franka`, and `Husarion ROSBot XL`.

#### Impact for users
All projects using `RosRobotSample` Gem will require an easy update: changing the required Gem name. All asset names and paths will remain unchanged.

## WarehouseAssets Gem

#### Summary
This Gem contains assets (meshes, textures and ready-to-use *prefabs*) that can be used to create a warehouse project suitable for robotic simulations.

#### Proposed changes
The meshes are not optimized for large warehouse environments and the warehouse elements have fixed sizes. Therefore, the proposed changes will focus on:
- reducing the number of vertices of meshes whenever possible and necessary
- modifying the sizes of warehouse elements to allow building warehouses of different dimensions

#### Impact for users
The existing *prefabs* will be modified in a way, to be interchangeable with the old ones. No actions from the users of `WarehouseAssets` will be needed.

## WarehouseAutomation Gem

#### Summary
This Gem contains assets (meshes, textures and ready-to-use *prefabs*) that can be used to create conveyor belts in warehouse projects suitable for robotic simulations. Additionally, it implements the physics of the conveyor belts and the proximity sensor. The Gem was implemented primarily for [ROSCon2023Demo](https://github.com/RobotecAI/ROSCon2023Demo) project. An example of its use can also be found in [ROS 2 Project Template](https://github.com/o3de/o3de-extras/tree/development/Templates/Ros2RoboticManipulationTemplate).

#### Proposed changes
The meshes and the implementation of the conveyors are not optimized for large environments, which makes it very difficult to use efficiently. We propose to remove the Gem completely and to move the meaningful elements to other Gems:
- `ConveyorElements` Assets: optimize and move the reusable assets to `WarehouseAssets` Gem
- `ConveyorLine` Assets: optimize and move the reusable assets to `WarehouseAssets` Gem
- `ConveyorBelt` implementation: remove (the physical implementation of the conveyor belt is too computationally expensive)
- `ProximitySensor` implementation: move to `ROS 2` Gem next to `ContactSensor`; make the behavior of the two similar

The detailed list of meshes, textures and *prefabs* that will be copied and removed will be compiled after an agreement to remove the Gem.

#### Impact for users
The users will have to adapt the projects accordingly or use the old version of the Gem. The impact is unknown as the popularity of the Gem is not known, however, the complexity of the Gem hints at the rather low usage. E.g. [ROSCon2023Demo](https://github.com/RobotecAI/ROSCon2023Demo) project contains duplicates of the assets for this reason. On the other hand, the implementation of the belt's physics is used and will have to be either ported or reimplemented.

## WarehouseSample Gem

#### Summary
This Gem contains assets (meshes, textures and ready-to-use *prefabs*) that can be used to create a very simple warehouse project suitable for robotic simulations.

#### Proposed changes
This Gem should be removed completely after `WarehouseAssets` Gem is optimized and can be used to generate a small and lightweight warehouse environment. This proposal is triggered by the fact that the assets in the Gem require some rework after the updates in the engine (in AssetProcessor).

#### Impact for users
The users will have to adapt the projects accordingly, or use the old version of the Gem (the old version contains errors when used with future O3DE 2505.x). The impact is unknown as the popularity of the Gem is not known, however, the low quality of the assets (and the incorrect scale) hints that the Gem is not used widely. We will create a set of *prefabs* with the same names in `WarehouseAssets` Gem to minimize the impact of this change.

## Ros2FleetRobotTemplate project Template

#### Summary
This template was created to allow a quick start of a robotic simulation in a warehouse environment with multiple autonomous mobile robots (AMRs). The template is built based on `WarehouseAssets` and `ProteusRobot` Gems.

#### Proposed changes
No changes other than updates triggered by the proposed changes in the `WarehouseAssets` and the newly created `RosRobotSamples` Gems.

## Ros2ProjectTemplate project Template

#### Summary
This template was created to allow a quick start of a robotic simulation. It was meant to be the quickest to build and consume the least resources. The template is built based on `WarehouseSample` and `RosRobotSample` Gems.

#### Proposed changes
The template should be completely recreated based on `WarehouseAssets` and the newly created `RosRobotSamples` Gems.
Alternatively, the template could be completely removed and added to `Ros2FleetRobotTemplate` as a *default* level (the `Ros2FleetRobotTemplate` would contain two levels, for single- and multi-robot simulations).

## Ros2RoboticManipulationTemplate project Template

#### Summary
This template was created to allow a quick start of a robotic simulation in a warehouse environment with two different robotic arms. The template includes a large collection of assets (meshes, textures and ready-to-use *prefabs*).

#### Proposed changes
There are two levels in the template: `RoboticManipulation` and `RoboticPalletization`. The template will be reduced to the first one only for simplicity. The level itself will be updated, and the assets will be moved to the other Gems for reusability when necessary. The second level, `RoboticPalletization`, will be removed alongside some assets used within this level. This will remove duplicates (e.g. the conveyor belts are also located in `WarehouseAutomation` Gem and further improved *Universal Robots UR10* robot is stored in the external [o3de-ur-robots-gem](https://github.com/RobotecAI/o3de-ur-robots-gem) repository). 

The more detailed list of changes in assets:
- `BoxToyPuzzle` - assets will be simplified (textures are too detailed) and kept in the template
- `ConveyorLine` - assets will be removed
- `Gripper` - assets will be moved to the newly created `RosRobotSamples` Gem
- `Room` - assets will be simplified (textures are too detailed), materials will be corrected (the room is not visually pleasant) and kept in the template
- `UR10` - assets will be removed
- `UrdfImporter/panda` - assets will be moved to the newly created `RosRobotSamples` Gem
- unused `DiffuseProbeGrids` will be removed
