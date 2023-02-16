# Shapes LKG Workflow Tests

## Background

### Dependent Components
A lot of other components depend on shape components to define geometry, for example:

- PhysX Shape Collider
- Recast Navigation PhysX Provider
- Terrain Height Gradient List
- Terrain Layer Spawner
- Terrain Macro Material
- Terrain Physics Heightfield Collider
- Terrain Surface Materials List
- Vegetation Altitude Filter
- Vegetation Layer Spawner
- Vegetation Layer Blocker
- Vegetation Shape Intersection Filter
- Diffuse Probe Grid
- Light
- Reflection Probe

### PhysX Shape Collider Component
The following shape components are supported by the PhysX Shape Collider component:

- Box
- Capsule
- Sphere
- Cylinder
- Polygon Prism
- Quad

Polygon Prism is special because it might not be convex, and is automatically split into convex parts for use with the PhysX Shape Collider component. Note that the PhysX Shape Collider component does not currently support modifying shapes at runtime.

### Translation Offsets
The following shapes support translation offsets (currently behind a feature flag - set "EnableShapeComponentTranslationOffset": true under "Amazon" -> "Preferences"):

- Axis Aligned Bounding Box
- Box
- Capsule
- Sphere

### Component Modes
The following shapes have component modes:

- Axis Aligned Bounding Box
- Box
- Capsule
- Sphere
- Polygon Prism
- Spline
- Tube Shape
- White Box

### Non-uniform Scale
The following shapes support non-uniform scale:
- Box
- Quad
- Polygon Prism

## Workflows
### Area: Editing shapes
**Docs:**
- [Shape Components](https://www.o3de.org/docs/user-guide/components/reference/shape/)
- [PhysX Shape Collider](https://www.o3de.org/docs/user-guide/components/reference/physx/shape-collider/)
- [Non-uniform Scale Component](https://www.o3de.org/docs/user-guide/components/reference/non-uniform-scale/non-uniform-scale/)

| Workflow | Requests | Things to Watch For |
| --- | --- | --- |
| Shapes can be edited and affect downstream components. | <ul><li> Test editing the dimensions and shape translation offsets (where applicable) for a variety of shapes. </li><li> Try editing shape properties both via the entity inspector and using component modes / manipulators. </li><li> Test with various combinations of entity scale, position, rotation and non-uniform scale (where applicable). </li><li> Test some components which depend on shapes and verify that their behavior is consistent with the placement and dimensions of the shapes. </li><li> Test that some script canvas functions (e.g. "Is Point Inside", "Distance From Point") give sensible results. </li></ul> | <ul><li> Shapes at runtime should be consistent with their editor definitions. </li><li> Debug drawing for the shapes should appear correct. </li><li> Visibility bounds should completely contain their shapes (bounds can be visualized in the editor using the ed_visibility_showAggregateEntityWorldBounds variable in the console). </li><li> Manipulators should always appear in the correct location relative to the shape and should update their positions correctly during editing. </li><li> Capsule shapes should always have height greater than or equal to twice their radius. </li></ul> |

