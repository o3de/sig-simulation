# AI LKG Workflow Tests

Testing in this area should focus on the AI functionality provided in the Recast Navigation gem.

## General Animation Docs
* [O3DE Dev Docs: Recast Navigation](https://deploy-preview-1693--o3deorg.netlify.app/docs/user-guide/interactivity/navigation-and-pathfinding/recast-navigation/)

## Common Issues to Watch For

Test guidance will sometimes note specific issues to watch for. The common issues below should be watched for through all testing, even if unrelated to the current workflow being tested.
- Asset processor errors when saving animation settings
- Warnings or Errors that appear in the Editor Console Log while setting up or running scenes.
- Errors appearing in the Animation Editor's Log Window view.
- Path Finding doesn't obey collisions.
- Path Finding debug draw does not project correctly.

## Workflows

### Area: Stress Test Workflow using Automated Testing sample

**Project Requirements**

* AutomatedTesting project


**Editor Platforms:**
* Windows
* Linux

**Game Launcher Supported Platforms:**
* Windows
* Linux
* Mobile

**Prerequisites:**
* You've read through the Recast Navigation docs
* AutomatedTesting is set as your default project and has been built for editor and game launcher
* Ability to deploy sample to Mobile device for mobile sample


**Product:** 

**Suggested Time Box:** 15 minutes per platform

| Workflow                      | Requests                                                                                                                                                                                                                       | Things to Watch For                                                                                                                                                                                                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Editor Mode Stress Test**   | <ol><li>Open the Editor with AutomatedTesting project.</li><li>Open Navigation/NavigationSample level</li><li>Enter the game mode with CTRL+G.</li><li>Left click around the level to navigate the character around.</li></ol> | <ul><li>Level opens without errors.</li><li>Player entity navigates around the map.</li><li>Verify that there is no performance issues while navigating.<ul><li>Extreme Stutter.</li><li>Framerate falls to 1 or some other unusable frame rate</li></ul></li></ul> |
| **Game Launcher Stress Test** | <ol><li>Open the AutomatedTesting game launcher.</li><li>Open Navigation/NavigationSample level</li><li>Left click around the level to navigate the character around.</li></ol>                                                | <ul><li>Level opens without errors.</li><li>Player entity navigates around the map.</li><li>Verify that there is no performance issues while navigating.<ul><li>Extreme Stutter.</li><li>Framerate falls to 1 or some other unusable frame rate</li></ul></li></ul> |
---

### Area: In Editor Rendering Workflow

**Project Requirements**
* Recast Navigation Gem has been enabled and built for the project (AutomatedTesting comes with this gem enabled)
* A level with at least two entities have been created


**Editor Platforms:**
* Windows
* Linux

**Prerequisites:**
* You've read through the Recast Navigation docs
* AutomatedTesting is set as your default project and has been built for editor and game launcher
* Ability to deploy sample to Mobile device for mobile sample


**Product:** 

**Suggested Time Box:** 30 minutes per platform

| Workflow                                                                  | Requests                                                                                                                                                                                                                                                                                                                                                                                                                   | Things to Watch For                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Configure NavMesh Entity**                                              | <ol><li>On the NavMesh entity add the following components:<ul><li>Recast Navigation mesh.<ul><li>Editor Preview: True</li></ul></li><li>Required: Recast Navigation PhysX Provider</li><li>Required: Axis Aligned Box Shape</li></uL></li></ol>                                                                                                                                                                           | <ul><li>Recast Nav Mesh component can be added to an entity and configured.</li><li>If required gems For Recast Nav Mesh component to work are not already on the Entity, the component will notify the user and provide them a path to adding the missing components.</li><li>Axis Aligned Box Shape can be added to the Entity.</li></ul> |
| **Configure White Box Collider Entity**                                   | <ol><li>On the Second Entity add the following components:<ul><li>White Box</li><li>White Box Collider</li></ul></li></ol>                                                                                                                                                                                                                                                                                                 | <ul><li>Components can be added to the entity.</li></ul>                                                                                                                                                                                                                                                                                    |
| **Set up and Manipulate the Scene to see in Editor Nav Mesh Projections** | <ol><li>Set up the Navigation Mesh and White Box entities to be intersecting.<ul><li>![](images\nav-mesh-whitebox-intersection.png)</li></ul></li><li>Manipulate White Box to create distinct angles and faces for the nav mesh to project to.<ul><li>![](images\whitebox-editing-navmesh-projection-change.gif)</li></ul></li><li>Change the values on the Recast Navigation mesh while modifying the white box</li></ol> | <ul><li>Nav Mesh is projected to the entity during editor preview.</li><li>Changes in the Recast Nav Mesh values reflect upn the editor preview projection</li></ul>                                                                                                                                                                        |
| **Explore Further**                                                       | <ol><li>The Second Entity can be set up to use PhysX Terrain, PhysX Collider, and PhysX Shape Colliders instead of White Box & White Box Colliders</li></ol>                                                                                                                                                                                                                                                               | <ul><li>Nav Mesh behaviors are also possible on supported components.</li></ul>                                                                                                                                                                                                                                                             |

---


## Additional Coverage: New Features, Feature Improvements, Areas of Concern for Current LKG
This section should change for each LKG cycle to target new features, feature area improvements, or an area that has been presenting issues and can use additional coverage in the LKG cycle.

Execute the following Workflow Docs:



