# Animation LKG Workflow Tests

Testing in this area should focus on the Animation functionality including the Animation Editor and Animation scene rendering.

## General Animation Docs
* [O3DE Animation Overview](https://www.o3de.org/docs/user-guide/visualization/animation/)
* [O3DE Getting Started With Animation Editor](https://www.o3de.org/docs/user-guide/visualization/animation/animation-editor/quick-start/)
* 

## Common Issues to Watch For

Test guidance will sometimes note specific issues to watch for. The common issues below should be watched for through all testing, even if unrelated to the current workflow being tested.
- Asset processor errors when saving animation settings
- Warnings or Errors that appear in the Editor Console Log while setting up or running scenes. 
- Broken Animation behaviors.



## Supported Component Variants
### PhysX Collider shapes
* Box
* Capsule
* Sphere
* PhysicsAsset

## Workflows

### Area: Animation Editor

**Project Requirements**

Any project that has the following Gems enabled: 
* Emotion FX Animation
* Scripted Entity Tweener
* Primitive Assets

**Platforms:**
* Windows
* Linux

**Docs:** 
* [O3DE Animation Editor User Interface](https://www.o3de.org/docs/user-guide/visualization/animation/animation-editor/user-interface/)
* [O3DE Getting Started With Animation Editor](https://www.o3de.org/docs/user-guide/visualization/animation/animation-editor/quick-start/)

**Product:** 

**Suggested Time Box:** 60 minutes

| Workflow                                     | Requests            | Things to Watch For |
|----------------------------------------------|---------------------|---------------------|
| **Launch and configure the Animation Editor** | <ol><li></li></ol>  | <ul><li></li></ul>  |
| **Import an Actor and render an animation**  | <ol><li></li></ol>  | <ul><li></li></ul>  |
| **Create a custom Workspace**                | <ol><li></li></ol>  | <ul><li></li></ul>  |
| **Create a custom Layout**                  | <ol><li></li></ol>  | <ul><li></li></ul>  |

---

### Area: Set up a scene where an actor will animate a scene then ragdoll

**Project Requirements**

Any project that has the following Gems enabled: 
* PhysX 
* Emotion FX Animation
* Scripted Entity Tweener
* Primitive Assets

**Platforms:**
* Windows
* Linux

**Docs:** 
* [O3DE Creating and Simulating](https://www.o3de.org/docs/user-guide/visualization/animation/animation-editor/creating-and-simulating-physx-ragdoll/)
* [O3DE PhysX Fixed Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/fixed-joint/)
* [O3DE PhysX Hinge Joint](https://www.o3de.org/docs/user-guide/components/reference/physx/hinge-joint/)

**Product:** A scene with PhysX Joint entities that test their ability to interact with a scene

**Suggested Time Box:** 60 minutes

| Workflow                                                                             | Requests            | Things to Watch For |
|--------------------------------------------------------------------------------------|---------------------|---------------------|
| **Import an Actor with joints and bones into animation editor an apply Ragdoll**     | <ol><li></li></ol>  | <ul><li></li></ul>  |
| **Add a blendree animation to the Actor**                                            | <ol><li></li></ol>  | <ul><li></li></ul>  |
| **Set up a scene where the actor performs a series of animations before Ragdolling** | <ol><li></li></ol>  | <ul><li></li></ul>  |
---



