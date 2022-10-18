# Translation Offset for Shape Components

### Summary

A translation offset will be added to certain shape components (primarily focusing on axis-aligned box, box, sphere and capsule), and manipulators for those shapes will be updated to take advantage of the new offset. This will remove the restriction that the shapes must be centered on the entity position, and make setting up shapes significantly easier for users.

### What is the relevance of this feature?

Shapes are often used to define regions for other components such as reflection probes or vegetation placement, and so are consumed by a very large number of other components. Currently, shapes must be centered at the position of the entity they are attached to. This can make it impossible to align a shape with a mesh or other feature on the same entity if the origin of that feature is not conveniently placed, often forcing users to add an extra entity in order to change the relative position of the shape. It also creates an awkward workflow of repeatedly switching between modifying shape dimensions and entity transforms.

This has led to numerous requests to make box shape components easier to adjust by allowing the faces to be moved asymmetrically. See for example

- [9883](https://github.com/o3de/o3de/issues/9883)
- [9672](https://github.com/o3de/o3de/issues/9672)

### Feature design description

- Certain shape components (the proposal is to focus on axis-aligned box, box, sphere and capsule initially as they are the most frequently used) will have a translation offset parameter added. Other shapes can be updated over time.
- All components which depend on affected shape components will be updated to handle translation offsets.
- Manipulators will be added/updated to allow the offsets to be edited on the affected components. The box shape will allow faces of the box to be moved independently, as well as translating the entire shape relative to the entity. The capsule shape will allow the two ends of the capsule to be moved independently, as well as translating the entire shape.

### Technical design description

#### Internal representation and API

Internally, the shape components will have a new translation offset field added. Although there are various ways the new data could potentially be parameterized, this has the advantage that if it defaults to (0, 0, 0), then no existing saved data will be broken. It can still be presented to the user using some other parameterization. For example, for a `BoxShapeComponent`, the offset can be presented as independently movable faces.

A getter and setter for the translation offset will be added to the `ShapeComponentRequestsBus` and the `OnShapeChanged` event in `ShapeComponentNotificationsBus` updated to fire when the translation offset changes.

Adding the offset will increase memory usage for shape components, but the additional memory requirement will be negligible (even for a million entities with shape components, only 16MB extra would be required). It is also tiny compared to the memory required to add another entity, as is often currently necessary to work around the lack of an offset.

#### Dependent components

The following components depend on one or more shape components. All of these components will be updated to work with translation offsets as part of this work, with no expectation of work from the affected SIGs other than reviewing PRs. Unit tests will be added and manual testing conducted to minimize the risk of introducing incorrect behavior to those components.

| Component                                        | Location               | SIG                | Notes   | AA Box | Box | Caps | Comp | Cyl | Disk | Prism | Quad | Ref | Sphere | Tube |
| ------------------------------------------------ | ---------------------- | ------------------ | ------- |:------:|:---:|:----:|:----:|:---:|:----:|:-----:|:----:|:---:|:------:|:----:|
| GradientSurfaceDataComponent                     | Gems\GradientSignal    | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| GradientTransformComponent                       | Gems\GradientSignal    | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| ShapeAreaFalloffGradientComponent                | Gems\GradientSignal    | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| SurfaceAltitudeGradientComponent                 | Gems\GradientSignal    | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| SurfaceDataShapeComponent                        | Gems\SurfaceData       | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| TerrainHeightGradientListComponent               | Gems\Terrain           | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| TerrainLayerSpawnerComponent                     | Gems\Terrain           | sig-content        |         | x      |     |      |      |     |      |       |      |     |        |      |
| TerrainMacroMaterialComponent                    | Gems\Terrain           | sig-content        |         | x      |     |      |      |     |      |       |      |     |        |      |
| TerrainPhysicsColliderComponent                  | Gems\Terrain           | sig-content        |         | x      |     |      |      |     |      |       |      |     |        |      |
| TerrainSurfaceMaterialsListComponent             | Gems\Terrain           | sig-content        |         | x      |     |      |      |     |      |       |      |     |        |      |
| SurfaceAltitudeFilterComponent                   | Gems\Vegetation        | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| SpawnerComponent                                 | Gems\Vegetation        | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| BlockerComponent                                 | Gems\Vegetation        | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| ShapeIntersectionFilterComponent                 | Gems\Vegetation        | sig-content        |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| RandomTimedSpawnerComponent                      | Gems\LmbrCentral       | sig-content        | Legacy? | x      | x   |      |      | x   | x    |       | x    | x   | x      |      |
| AudioAreaEnvironmentComponent                    | Gems\LmbrCentral       | sig-graphics-audio | Legacy? | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| DiffuseProbeGridComponentController              | Gems\DiffuseProbeGrid  | sig-graphics-audio |         |        | x   |      |      |     |      |       |      |     |        |      |
| AreaLightComponentController                     | Gems\AtomLyIntegration | sig-graphics-audio |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| ReflectionProbeComponentController               | Gems\AtomLyIntegration | sig-graphics-audio |         |        | x   |      |      |     |      |       |      |     |        |      |
| ShapeWeightModifierComponentController           | Gems\AtomLyIntegration | sig-graphics-audio |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| BaseColliderComponent                            | Gems\PhysX             | sig-simulation     |         | x      | x   | x    | x    | x   | x    | x     | x    | x   | x      | x    |
| EditorShapeColliderComponent                     | Gems\PhysX             | sig-simulation     |         |        | x   | x    |      | x   |      | x     | x    |     | x      |      |
| EditorNavigationAreaComponent                    | Gems\LmbrCentral       | sig-simulation     | Legacy? |        |     |      |      |     |      | x     |      |     |        |      |
| RecastNavigationPhysXProviderComponentController | Gems\RecastNavigation  | sig-simulation     |         | x      |     |      |      |     |      |       |      |     |        |      |

#### Debug Draw and Visibility
Debug draw for the affected shape components will be updated to render the shapes in the correct location relative to their entities. The implementations of BoundsRequests::GetWorldBounds and ShapeComponentRequests::GetEncompassingAabb will be updated to ensure correct entity visibility.

#### Non-uniform scale

Some shapes are currently marked as compatible with the non-uniform scale component:

- Box
- Quad
- Polygon Prism

Additional care should be taken (e.g. writing extra unit tests) with the box shape to ensure that non-uniform scale is correctly applied with respect to the translation offset.

### What are the advantages of the feature?

- Users will be able to position shapes relative to their entities, removing the frequent need to create additional entities just to handle the offset.
- Users will be able to set up shape components significantly more easily.

### What are the disadvantages of the feature?

- It could be a slightly worse experience if the user wants symmetrical editing. Two mouse operations or a more complex mouse operation with modifier key could be required to perform symmetrical editing. This compares to a very bad current experience of having to switch repeatedly between editing the entity transform and the shape dimensions.

### How will this be implemented or integrated into the O3DE environment?

- This proposal just updates the existing internal shape components, no further integration is necessary.

### Are there any alternatives to this feature?

- No, other then continuing with the status quo.

### How will users learn this feature?

- Through the component modes and manipulators for the affected shape components.

### Are there any open questions?

- What about rotation offsets? This would be problematic for several reasons:
  - Additional scope.
  - Can't work with shapes which current support non-uniform scale, and would be a breaking change for any existing serialized data which uses e.g. box shape with non-uniform scale.
  - Less requested as a feature versus translation offsets.
- Should modifier keys be supported (e.g. to allow symmetrical editing)? If so, how will they be discovered?
