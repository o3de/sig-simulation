# Option to disable inheriting parent's transform at runtime

### Summary
A setting would be added to the transform component which allows turning off the inheritance of transform information from a parent (at runtime; the inheritance behaviour will still be desirable at edit time). The setting would be available via the transform interface, and would allow other components to switch off that behaviour if they will be responsible for controlling an entity's transform.

### What is the relevance of this feature?
Certain components are intended to control the transform of an entity, such as:

- Rigid body component
- Actor component
- Attachment component (a component which connects an entity to a joint on an actor e.g. so you can pick up an item and have it move with the character's hand)

If an entity is a child of another entity, then its transform will also update when the parent moves, and the two transform updates can end up fighting. This leads to a couple of problems:

- If components like the ones mentioned above are on a child entity, it will likely lead to unexpected behaviour, the cause of which is not obvious, hard to diagnose and generally not documented.
- The current easiest way to avoid problems is to only put entities with affected components at root level (or at least not children of moving parents), but that itself has some problems:
  - There's no guidance at the moment to know that's what you need to do, either in documentation or UI.
  - It puts a restriction on how you organize your entities. There are a lot of situations where it's natural from an organizational perspective to make entities children (e.g. wheels as child entities in a vehicle). Forcing those entities to live at root level could also lead to very flat hierarchies in the entity outliner which are hard to work with because they don't have a nice hierarchical structure.
  - Certain workflows are awkward, for example authoring a PhysX joint, because the follower cannot be a child of the lead, which means that it does not move with the lead in the editor.

### Feature design description

### Technical design description
A setting will be added to the transform component, accessible via the transform interface. This proposal does not include exposing that setting on the transform component's component card, but that could be done as a separate, future proposal.

### What are the advantages of the feature?
It allows objects which may be represented as multiple entities (e.g. vehicles, articulations) to have a representation in the entity hierarchy which reflects their logical organization.

### What are the disadvantages of the feature?
Currently there is a very simple rule for how the hierarchy affects an entity's transform. This proposal could make it harder to reason about expected transform behaviour.

### Are there any alternatives to this feature?
- Stick with the current inheritance behaviour
  - For physical hierarchies such as articulations or vehicles, users would have to continue authoring as flat entity hierarchies which do not reflect the logical organization.
  - If we did go for this approach, we ought to add improved documentation on hierarchy patterns to avoid or automatically disable affected components and issue a warning if problematic hierarchies are detected.

### How will users learn this feature?
The intent is that the feature will be largely invisible to most users. For specific use cases such as articulations, it could be communicated through tutorials or demo content.

### Are there any open questions?