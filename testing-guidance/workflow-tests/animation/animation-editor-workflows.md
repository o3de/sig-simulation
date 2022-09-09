# FEATURE Workflow Tests

Testing in this area should focus on the SOMETHING SOMETHING.

## General Docs
* [Some Docs](https://www.o3de.org/docs/)

## Common Issues to Watch For

Test guidance will sometimes note specific issues to watch for. The common issues below should be watched for through all testing, even if unrelated to the current workflow being tested.
- Some common issue

## Workflows

### Area: Animation Editor

**Project Requirements**

Any project that has the following Gems enabled: 
* Emotion FX Animation
* Scripted Entity Tweener
* Primitive Assets

Assets:
* An Actor that is set up to work with animations.
* Animations for your Actor.


**Platforms:**
* Windows
* Linux

**Docs:** 
* [O3DE Animation Editor User Interface](https://www.o3de.org/docs/user-guide/visualization/animation/animation-editor/user-interface/)
* [O3DE Getting Started With Animation Editor](https://www.o3de.org/docs/user-guide/visualization/animation/animation-editor/quick-start/)

**Product:** 

**Suggested Time Box:** 60 minutes

| Workflow                                                          | Requests                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Things to Watch For                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Launch and configure the Animation Editor**                     | <ol><li>Open the O3DE Editor.</li><li>Open the Animation Editor (using varying entry points):</li><ul><li>O3DE Toolbar → Tools → Animation Editor</li><li>Actor Component's Actor asset property field.</li></ul><li>Resize the Animation Editor to desired size.</li><li>Dock/Undock editor to desired position.</li><li>Select the desired Layout.</li><li>Move Animation Editor panels around within the Animation Editor</li><li>Open and close Separator Widgets on various panels.<ul><li>Joint Outliner</li><li>Motion Sets</li><li>Motion Events</li><li>Actor Manager</li></ul></li></ol> | <ul><li>Animation Editor launches</li><li>Animation Editor Resizes.</li><li>Animation Editor docks as expected.</li><li>Layouts will quickly switch between different View configurations.</li><li>Separator widgets can be collapsed and expanded while the parent layout resizes accordingly. [GH-10958](https://github.com/o3de/o3de/issues/10958), [GH-10960](https://github.com/o3de/o3de/issues/10960) <ul><li>![](images/separator_widget.gif) </li></ul></li></ul>                                                                                                                                                                                                                      |
| **Import an Actor and render an animation using various cameras** | <ol><li>Open the Animation Editor.</li><li>Import an Actor.</li><li>Use the Actor Manager to set the Motion Extraction Joint.</li><li>In Motion Sets add the motions you want to play and save the motion sets you created.</li><li>Select the motion that you want to play and click play in the Time View.</li><li>While animation plays switch between cameras.</li><li>Save the Actor.</li></ol>                                                                                                                                                                                               | <ul><li>Actor Imports without any issues.</li><li>Joint is set when selected.</li><li>When Selecting a joint and using Find Best Match, a joint is selected.</li><li>Motion Sets Can be Created, Updated, Deleted.</li><li>Motions can be added, updated, and deleted to/from motion sets.</li><li>Motion Sets save and process in the Asset Processor.</li><li>Animation Editor's Viewport renders the animation when the motion is played and stops playing when stopped.</li><li>When another animation is played, the previously playing animations stops immediately before playing the next animation.</li><li>Animation render plays as expected while the camera views change.</li></ul> |
| **Create and load a custom Workspace**                            | <ol><li>Open the Animation Editor.</li><li>Create a partial actor workflow.</li><li>Save the workspace and close out of the Animation Editor.</li><li>Open the Animation Editor back up a previously created workspace.</li></ol>                                                                                                                                                                                                                                                                                                                                                                  | <ul><li>Workspace saves and is processed by the Asset Processor.</li><li>Animation Editor loads in the Actor, Motion Sets, Motions, and other modified fields.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Create a custom Layout**                                        | <ol><li>Open the Animation Editor.</li><li>Close some views.</li><li>Open some unopened views.</li><li>Move and resize views around within the Animation Editor.</li><li>Save the Layout.</li><li>Switch layouts back and forth.</li></ol>                                                                                                                                                                                                                                                                                                                                                         | <ul><li>Views open and close in the Animation Editor.</li><li>Views move around and resize within animation editor as expected.</li><li>View Saves successfully.</li><li>When switching between Layouts, the views load according to the desired view, including user generated views.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                |

---


## Additional Coverage: New Features, Feature Improvements, Areas of Concern for Current LKG
This section should change for each LKG cycle to target new features, feature area improvements, or an area that has been presenting issues and can use additional coverage in the LKG cycle.

Execute the following Workflow Docs:



