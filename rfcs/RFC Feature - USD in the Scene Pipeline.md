*   1[Opportunity & Background Info](#USDintheScenePipeline-Opportunity&BackgroundInfo)
    *   1.1[Opportunity](#USDintheScenePipeline-Opportunity)
        *   1.1.1[What are scene files?](#USDintheScenePipeline-Whatarescenefiles?)
        *   1.1.2[What makes USD interesting, why use USD instead of another format like FBX, GLTF, STL, Collada, STEP, etc?](#USDintheScenePipeline-WhatmakesUSDinteresting,whyuseUSDinsteadofanotherformatlikeFBX,GLTF,STL,Collada,STEP,etc?)
    *   1.2[Required O3DE Knowledge](#USDintheScenePipeline-RequiredO3DEKnowledge)
    *   1.3[USD Terminology](#USDintheScenePipeline-USDTerminology)
    *   1.4[USD Basics](#USDintheScenePipeline-USDBasics)
        *   1.4.1[Basic USD file](#USDintheScenePipeline-BasicUSDfile)
        *   1.4.2[Basic USD file references](#USDintheScenePipeline-BasicUSDfilereferences)
        *   1.4.3[Beyond the Basics](#USDintheScenePipeline-BeyondtheBasics)
*   2[Recommended Solution](#USDintheScenePipeline-RecommendedSolution)
    *   2.1[Solution Overview](#USDintheScenePipeline-SolutionOverview)
        *   2.1.1[Why these three phases?](#USDintheScenePipeline-Whythesethreephases?)
        *   2.1.2[Simple Example](#USDintheScenePipeline-SimpleExample)
        *   2.1.3[Example with References](#USDintheScenePipeline-ExamplewithReferences)
    *   2.2[Solution in Detail](#USDintheScenePipeline-SolutioninDetail)
        *   2.2.1[Phase 1 - Migrate to latest Assimp and treat USD like other scene formats](#USDintheScenePipeline-Phase1-MigratetolatestAssimpandtreatUSDlikeothersceneformats)
        *   2.2.2[Phase 2 - Update Assimp to support flattened USD with references](#USDintheScenePipeline-Phase2-UpdateAssimptosupportflattenedUSDwithreferences)
            *   2.2.2.1[The Current Setup](#USDintheScenePipeline-TheCurrentSetup)
            *   2.2.2.2[After Phase 2, USD files can reference other USD or OBJ meshes](#USDintheScenePipeline-AfterPhase2,USDfilescanreferenceotherUSDorOBJmeshes)
        *   2.2.3[Phase 3 - Prefab output mimicking USD relationship graph](#USDintheScenePipeline-Phase3-PrefaboutputmimickingUSDrelationshipgraph)
*   3[Alternate Solutions](#USDintheScenePipeline-AlternateSolutions)
    *   3.1[Integrate Pixar USD Library Instead of Using Assimp](#USDintheScenePipeline-IntegratePixarUSDLibraryInsteadofUsingAssimp)
*   4[Open Questions](#USDintheScenePipeline-OpenQuestions)
    *   4.1[Should we move Assimp to a Gem instead of using O3DE 3rdParty system?](#USDintheScenePipeline-ShouldwemoveAssimptoaGeminsteadofusingO3DE3rdPartysystem?)
    *   4.2[Should we fail a job, or emit an error if a reference to another USD file can't be resolved?](#USDintheScenePipeline-Shouldwefailajob,oremitanerrorifareferencetoanotherUSDfilecan'tberesolved?)
    *   4.3[Do we need to emit references to other USD files as job dependencies, or can they just be product dependencies?](#USDintheScenePipeline-DoweneedtoemitreferencestootherUSDfilesasjobdependencies,orcantheyjustbeproductdependencies?)
        *   4.3.1[Example](#USDintheScenePipeline-Example)
    *   4.4[Why not create a new USD builder upstream of the Scene Builder, and use intermediate assets as a middle step?](#USDintheScenePipeline-WhynotcreateanewUSDbuilderupstreamoftheSceneBuilder,anduseintermediateassetsasamiddlestep?)
    *   4.5[Can we put most of this logic into a Gem, instead of directly updating the scene API?](#USDintheScenePipeline-CanweputmostofthislogicintoaGem,insteadofdirectlyupdatingthesceneAPI?)
    *   4.6[Do we need to update the scene API to support loading multiple scenes from the same file?](#USDintheScenePipeline-DoweneedtoupdatethesceneAPItosupportloadingmultiplescenesfromthesamefile?)
    *   4.7[How configurable can we make the prefab generation?](#USDintheScenePipeline-Howconfigurablecanwemaketheprefabgeneration?)
    *   4.8[What do we need from procedural prefabs for USD to be fully functional?](#USDintheScenePipeline-WhatdoweneedfromproceduralprefabsforUSDtobefullyfunctional?)
        *   4.8.1[New Entity Context?](#USDintheScenePipeline-NewEntityContext?)
        *   4.8.2[Abstract Prefabs](#USDintheScenePipeline-AbstractPrefabs)
    *   4.9[USD files referencing non-USD file?](#USDintheScenePipeline-USDfilesreferencingnon-USDfile?)
    *   4.10[Do all USD overrides work with prefabs, or will we have to special case some?](#USDintheScenePipeline-DoallUSDoverridesworkwithprefabs,orwillwehavetospecialcasesome?)
    *   4.11[Would it make sense to do this in phases, flattening in the first part?](#USDintheScenePipeline-Woulditmakesensetodothisinphases,flatteninginthefirstpart?)
    *   4.12[When should we do this?](#USDintheScenePipeline-Whenshouldwedothis?)
*   5[Out of Scope / Long Term Work](#USDintheScenePipeline-OutofScope/LongTermWork)
    *   5.1[Advanced shaders and materials](#USDintheScenePipeline-Advancedshadersandmaterials)
    *   5.2[Convert O3DE prefabs to USD files](#USDintheScenePipeline-ConvertO3DEprefabstoUSDfiles)
*   6[FAQ](#USDintheScenePipeline-FAQ)
    *   6.1[Why not stop after phase 2: just treat USD like other formats, flatten everything, and then generate product assets?](#USDintheScenePipeline-Whynotstopafterphase2:justtreatUSDlikeotherformats,flatteneverything,andthengenerateproductassets?)
    *   6.2[Why do we need both source dependencies and job dependencies for references?](#USDintheScenePipeline-Whydoweneedbothsourcedependenciesandjobdependenciesforreferences?)
    *   6.3[Why build USD support into the Scene Builder, why not create a new USD builder?](#USDintheScenePipeline-WhybuildUSDsupportintotheSceneBuilder,whynotcreateanewUSDbuilder?)
    *   6.4[Why use an existing library for loading USD files, instead of rolling our own solution?](#USDintheScenePipeline-WhyuseanexistinglibraryforloadingUSDfiles,insteadofrollingourownsolution?)
    *   6.5[Are there other reasons for customers to want to use USD over our existing, supported scene file formats?](#USDintheScenePipeline-ArethereotherreasonsforcustomerstowanttouseUSDoverourexisting,supportedscenefileformats?)
        *   6.5.1[FBX versus USD](#USDintheScenePipeline-FBXversusUSD)
        *   6.5.2[FBX versus GLTF](#USDintheScenePipeline-FBXversusGLTF)
    *   6.6[Would we drop support for other scene file formats with USD support? Why wouldn't we want to only support USD?](#USDintheScenePipeline-WouldwedropsupportforotherscenefileformatswithUSDsupport?Whywouldn'twewanttoonlysupportUSD?)
    *   6.7[Are there other benefits to supporting USD this way?](#USDintheScenePipeline-ArethereotherbenefitstosupportingUSDthisway?)

Opportunity & Background Info
=============================

Opportunity
-----------

The [Universal Scene Description](https://graphics.pixar.com/usd/release/index.html) (USD) file format is frequently requested to be a supported scene format, as an addition to the FBX, GTLF, and STL that we already support.

### What are scene files?

Scene files describe 3D content: Geometry, materials, animations. They also include transforms and hierarchy information, as well as generic nodes, and user defined properties.

### What makes USD interesting, why use USD instead of another format like FBX, GLTF, STL, Collada, STEP, etc?

USD is built around a composition feature, the ability to easily reference other files. It's a lot like our prefab system.
![image](https://github.com/user-attachments/assets/397b92ed-f43c-433e-8cc5-81ada05d33c8)

Required O3DE Knowledge
-----------------------

*   Familiarity with the O3DE scene pipeline.
    *   [https://www.o3de.org/docs/user-guide/assets/scene-pipeline/](https://www.o3de.org/docs/user-guide/assets/scene-pipeline/)
*   Familiarity with the O3DE asset pipeline.
    *   [https://www.o3de.org/docs/user-guide/assets/pipeline/](https://www.o3de.org/docs/user-guide/assets/pipeline/)
*   Familiarity with O3DE Assimp integration.
    *   [o3de/Code/Tools/SceneAPI/SDKWrapper/AssImpSceneWrapper.cpp at bbc8fa583c34186a9c3ede51b2d6254701c4afca · o3de/o3de (github.com)](https://github.com/o3de/o3de/blob/bbc8fa583c34186a9c3ede51b2d6254701c4afca/Code/Tools/SceneAPI/SDKWrapper/AssImpSceneWrapper.cpp#L91)
    *   Note: You can debug AssetBuilder.exe by enabling --debug and providing USD asset path. (Must have AssetProcessor.exe running)

        ```
        --project-path="D:/prj/o3de/AutomatedTesting" --debug "D:\prj\o3de\AutomatedTesting\Assets\asset.usd" --platform pc --tags dx12 --project-name AutomatedTesting --project-cache-path "D:\prj\o3de\AutomatedTesting\Cache"
        ```

USD Terminology
---------------

*   USD - Universal Scene Description - The scene file format under discussion here.
    *   [https://graphics.pixar.com/usd/release/index.html](https://graphics.pixar.com/usd/release/index.html)
*   USD specific terms and concepts
    *   [https://graphics.pixar.com/usd/release/glossary.html](https://graphics.pixar.com/usd/release/glossary.html)
    *   At minimum, you should be familiar with:
        *   Prim -The basic building block of USD content.
            *   [https://graphics.pixar.com/usd/release/glossary.html#usdglossary-prim](https://graphics.pixar.com/usd/release/glossary.html#usdglossary-prim)
        *   Asset - USD's system for referencing other file.
            *   [https://graphics.pixar.com/usd/release/glossary.html#asset](https://graphics.pixar.com/usd/release/glossary.html#asset)
        *   Composition Arcs & Composition - USD's system for resolving references, similar to our nested prefab system.
            *   [https://graphics.pixar.com/usd/release/glossary.html#usdglossary-compositionarcs](https://graphics.pixar.com/usd/release/glossary.html#usdglossary-compositionarcs)
        *   Stage - A composed USD file.
            *   [https://graphics.pixar.com/usd/release/glossary.html#usdglossary-stage](https://graphics.pixar.com/usd/release/glossary.html#usdglossary-stage)
*   USDA - File extension - Text based USD files.
*   USDC - File extension - Binary USD files.
*   USDZ - File extension - Zipped container of multiple files, intended to group USD content together.

USD Basics
----------

### Basic USD file

This usda file represents a stage that defines two prims: A transform named "hello" and a sphere child of that transform named "world".
```
#usda 1.0

def Xform "hello"
{
    def Sphere "world"
    {
    }
}
```

Visual representation of this file:
![image](https://github.com/user-attachments/assets/8a08df9e-213a-4545-a93d-43da8a49b16c)

### Basic USD file references

This pair of usda files, from [https://graphics.pixar.com/usd/release/tut\_referencing\_layers.html](https://graphics.pixar.com/usd/release/tut_referencing_layers.html), demonstrates how references work between USD files.

_Below: RefExample.usda references HelloWorld.usda and overrides world's color._

_HelloWorld.usda_
```
#usda 1.0
(
    defaultPrim = "hello"
)

def Xform "hello"
{
    double3 xformOp:translate = (4, 5, 6)
    uniform token[] xformOpOrder = ["xformOp:translate"]

    def Sphere "world"
    {
        float3[] extent = [(-2, -2, -2), (2, 2, 2)]
        color3f[] primvars:displayColor = [(0, 0, 1)]
        double radius = 2
    }
}
```

_RefExample.usda_
```
#usda 1.0
over "refSphere2" (
    prepend references = @./HelloWorld.usda@)
    {
        over "world"
        {
            color3f[] primvars:displayColor = [(1, 0, 0)]
        }
    }
```
  

### Beyond the Basics

I recommend reading through the USD tutorials here [https://graphics.pixar.com/usd/release/tut\_usd\_tutorials.html](https://graphics.pixar.com/usd/release/tut_usd_tutorials.html), and experimenting in Blender and Nvidia USD Composer with creating or importing scenes and exporting to usda format files.

Note: Blender does not support references:  
_"Like the USD exporter, the importer does not yet handle more advanced USD concepts, such as layers and references." [Universal Scene Description - Blender 4.2 Manual](https://docs.blender.org/manual/en/latest/files/import_export/usd.html)_

Digital content creation (DCC) tools which takes advantage of USD references are Nvidia Isaac Sim, Nvidia USD Composer, and Pixar tooling.  

Recommended Solution
====================

Solution Overview
-----------------

Build support for USD into the scene pipeline.

Do this in three phases:

1.  Single USD
    1.  Upgrade O3DE to use Assimp's latest implementation
    2.  Input: single USD geometry files that don't use layers or references
    3.  Output: azmodel containing the USDs geometry 
2.  USD with References
    1.  Update Assimp scene tree with tags for referenced USD files 
    2.  Update O3DE scene builder to load USD files and look for reference tags.
    3.  Update O3DE to create a procedural prefab that mimics the relationship graph of USD files by matching models and transforms
    4.  Input: USD files which contains a hierarchy of references to other geometry inside of other USD files 
    5.  Output: Procedural prefabs generated for the root USD as well as referenced USDs. Root prefab preserves the hierarchy of the original USD.
3.  USD with Variants 
    1.  Updated Assimp scene tree with tags for variants within a USD
    2.  Update O3DE scene builder to load USD files and look for variants
    3.  Input: USD file which contains X variants
    4.  Output: 
            1. X meshes and X prefabs generated for each variant
            2. Prefabs which references a variant in its hierarchy will nest 1 of the prefab variants

### Why these three phases?

*   Phase 1 is extremely fast.
*   Once phase 2 is completed, there's great benefits to using USD: content creators can start splitting up content on their end.
*   Benefits of phase 2:
    *   Performance - asset processing time, and hard drive space used by product assets: We won't need to re-create product assets, such as models, that are referenced by multiple other USD files.
    *   Workflow - additional procedural prefabs will be available for content creators to use in the Editor.
*   Phase 3 and beyond nice-to-have. 
    *   Variants are advanced so makes sense to break them out into a later phase.
    *   Avoid variant explosion by only selecting 1 of many variants when generating procedural prefabs

### Simple Example
![image](https://github.com/user-attachments/assets/2eaf9c33-0fb0-403a-a954-837695c91c6f)

### Example with References
![image](https://github.com/user-attachments/assets/ee794185-f07b-4c23-bbb1-99f5c2b94ea7)

Solution in Detail
------------------

### Phase 1 - Migrate to latest Assimp and treat USD like other scene formats

[AssImp now supports USD](https://github.com/assimp/assimp/issues/5547) without composition (no references) meaning that single asset usd, usda, usdc, and usdz files can be loaded.

1.  Update O3DE 3rdParty to use Assimp's latest library
    1.  There's no work required in O3DE code, just updating 3rdParty to use the latest Assimp release.
    2.  Note: The current Assimp release 5.4.2 from July 2024 doesn't contain USD. O3DE 3rdParty package builder [relies on git release tags](https://github.com/aws-lumberyard-dev/3p-package-source/blob/main/package-system/assimp/build_config.json#L3). If we want USD support now without waiting for the next release, the packaging script will need to be updated to _git pull_ this [specific commit](https://github.com/assimp/assimp/commit/0cb169368956fba4508fbd3af361cf37c8060e8e).
    3.  Note: [AssImp latest has compiler error](https://github.com/assimp/assimp/pull/5693)
2.  Add USD File Types to Asset Importer Settings
    1.  Edit _Registry/sceneassetimporter.setreg_ to process _usd, usda, usdz, usdc_ file types.
    2.  ![image](https://github.com/user-attachments/assets/fb1a4fea-c13b-4e0d-9044-58374b2b6d4c)

3.  Run Asset Processor
    1.  Allow asset processor to digest USD files, such as the toy\_biplane.usd file here.
    2.  [![](@todo_image)toy\_biplane\_idle.usdz](@todo_image)
    3.  ![image](https://github.com/user-attachments/assets/6eeacba2-afe2-4f22-95a2-0dc9b210a917)


At this point, USDs containing just geometry will load, however AssetProcessor will fail to create a product for a simple USD which references that very same geom file.

For example, the _suzanne.usdc_ geometry file will be processed correctly and generate a _cache/pc/suzanne.azmodel_, but _usdObj-001.usda_ won't produce anything in the cache folder.

_usdObj-001.usda which references a separate suzanne.usd geometry file._

```
def Mesh "suzanne" (
prepend references = @suzanne.usdc@</Suzanne/Suzanne>
)
{}
```
  

[![](@todo_image)suzanne.usdc](@todo_image) [![](@todo_image)usdObj-001.usda](@todo_image)
![image](https://github.com/user-attachments/assets/08feb54f-61e1-4faf-ac98-864a0d3f85b0)

```
Warning | The builder (Scene Builder) has not indicated it handled outputting product dependencies for file Assets/usdObj-001.usda.  This is a programmer error.
```

Phase 2 enables referencing...

### Phase 2 - Update Assimp to support USD with references

Update Assimp to read USDs, composite all sub-layers and tag references in Assimp scene. O3DE will parse the Assimp tree to find references and spin off procedural prefabs.

#### The Current Setup

Currently, scene files such as FBX store all their sub-meshes in a single FBX.

The Assimp scene builder returns the sub-meshes and O3D breaks them out into separate entities. 

Current: FBX Scene File to O3DE Entity

![image](https://github.com/user-attachments/assets/7ccf90e4-ec91-42fe-bbef-5f40b3c807c0)


#### After Phase 2, USD files can reference other USD or OBJ meshes

While ultimately the same O3DE entity hierarchy is produced, notice how unlike before, the car asset references external files (_carbody.usd_ and _tire.obj_) allowing multiple content creators to work at the same time.

_Car.usd_

```
def Xform "Xform"
{
    def Mesh "carbody" (
        prepend references = @carbody.usdc@</carbody>){}

    def Mesh "tire_1" (
        prepend references = @tire.obj@){
            double3 xformOp:translate = (1, 0, 0)
        }

    def Mesh "tire_2" (
        prepend references = @tire.obj@){
            double3 xformOp:translate = (0, 1, 0)
        }
    ...
}
```

![image](https://github.com/user-attachments/assets/9c5f4ddd-101b-4b2f-8da8-66daffbd2340)
1.  Update Assimp to support some passthroughs we would need for USD support, specifically a way to override asset resolving.
    1.  See USD documentation on Asset Resolution:
        1.  [https://graphics.pixar.com/usd/release/glossary.html#usdglossary-assetresolution](https://graphics.pixar.com/usd/release/glossary.html#usdglossary-assetresolution)
        2.  [https://graphics.pixar.com/usd/release/api/ar\_page\_front.html](https://graphics.pixar.com/usd/release/api/ar_page_front.html)
2.  Update Assimp to generate an Assimp scene containing a way to recognize references
    1.  Today a USD loaded via Assimp returns a scene, but if that scene references other USD files, the scene won't contain the geometry of the references. Update Assimp to recursively find references and tag them as a reference (include necessary data such as file path, and reference name)
    1.  Assimp outputs the scene graph like it currently does, but when it hits a reference, it annotates that node in the scene graph with a property that describes the reference.
        1.  Assimp::Scene root "kitchen"
            1.  child 01
                1.  Name: "spoon 01"
                2.  Transform: <translation>, <rotation>, <scale>
                3.  Reference: "c:/project/spoon.usd"
        
        TinyUSDZ, the 3rd Party USD library used by Assimp, has methods for discovering references geometry.
        
        [TinyUSDZ tusdcat code example](https://github.com/lighttransport/tinyusdz/blob/83b4ebc90761e60444e55611238fff6cfb6a62c9/examples/tusdcat/main.cc) is able to extract USD references as follows:
        
        1.  [Load USD as a Layer](https://github.com/lighttransport/tinyusdz/blob/83b4ebc90761e60444e55611238fff6cfb6a62c9/examples/tusdcat/main.cc#L149)
        2.  [Do compositions (it also returns a layer)](https://github.com/lighttransport/tinyusdz/blob/83b4ebc90761e60444e55611238fff6cfb6a62c9/examples/tusdcat/main.cc#L212)
        3.  Then [construct Stage from Layer](https://github.com/lighttransport/tinyusdz/blob/910c5740008377976a559c4ff1680292475abc85/src/composition.hh#L210) 

            ```
            bool LayerToStage(const Layer &layer, Stage *stage
            ```
            
        4.  Then call  `tinyusdz::tydra::ListPrims `
            ```
            std::map<std::string, const tinyusdz::GeomMesh*> meshmap;
            tinyusdz::tydra::ListPrims(stage, meshmap);
            ```
            Note: USD can reference other USDs or geometry from other file types (example: obj). As long as the file type is supported by Assimp, Assimp can tag it, and O3DE can load the geom and add it to the procedural prefab.
            
        
        More Information: [Accessing Reference File Geom · Issue #186 · lighttransport/tinyusdz (github.com)](https://github.com/lighttransport/tinyusdz/issues/186)
        
3.  From here, the current O3DE pipeline would take over, we would consume this scene.
    1.  ![image](https://github.com/user-attachments/assets/e4aac22e-0ec1-463a-96a6-a2b3fbfe7ec7)
    2.  Update Create Jobs in the scene builder to output job dependencies on referenced USD files.
    3.  [See FAQ for why we need both source and job dependencies](#USDintheScenePipeline-Whydoweneedbothsourcedependenciesandjobdependenciesforreferences?).
    4.  Job dependencies will be needed at this point because product assets from one USD file (prefabs, models, etc) will be referenced by other USD files to mimic the USD relationship graph.
    5.  Update the scene builder to generate USD based procedural prefabs.  
        1.  We will need to define what these look like. [We may need additional features from procedural prefabs to hit our end goals](https://wiki.agscollab.com/display/lmbr/USD+in+the+Scene+Pipeline#USDintheScenePipeline-WhatdoweneedfromproceduralprefabsforUSDtobefullyfunctional?).

### Phase 3 - Update Assimp to support USD with Variants

Variants are switchable references. Ideally, an O3DE component could provide a drop down in editor to quickly select from a list of variants within a variant set, but this phase simply recognizes variants in order to generate a mesh and prefab for each. Any root USD referencing a variant set will make a single variant selection.
1.  Update Assimp to generate an Assimp scene containing a way to recognize variant sets, via tagging
    1.  Assimp outputs the scene graph like it currently does, but when it hits a variant, it annotates that node in the scene graph with a property.
        1.  Assimp::Scene root "kitchen"
            1.  child 01
                1.  Name: "spoon 01"
                2.  Transform: <translation>, <rotation>, <scale>
                3.  Reference: "c:/project/spoon.usd"
                4.  Variant: "WoodenSpoonA"
2.  O3DE pipeline would consume this scene
    1.  Update scene builder to look for "variant set" and "variant" tags
    2.  Update scene builder to generate a procedural prefab for each variant in a variant set
    3.  Update scene builder so root USD prefabs referencing a variant set (referenced inline or another .usd file) will have a variant selected for them in their procedural prefab
        1.  In instances where the USD calls out a variant set, but no selection, the 1st variant in the variant set list will be selected
    4.  Do not try to combine
        1.  A single chair.usd becomes:
            1.  chair.procprefab 
            2.  chairback_01.procprefab -> chairback_01.atommesh
                ...
            3.  chaircushion_01.procprefab -> chaircushion_01.atommesh

Note: Variants are not always meshes, they can also just be basic overrides for transforms, material properties, etc. 
We should support transforms, but material properties and beyond will wait for future roadmap.


Alternate Solutions
===================

Integrate Pixar USD Library Instead of Using Assimp
---------------------------------------------------

*   Add the Pixar USD library to O3DE as a 3rd party dependency, update Scene Builder to reference this library.
    *   [See FAQ for why we want to update the scene builder for this support, instead of creating a new builder.](https://wiki.agscollab.com/display/lmbr/USD+in+the+Scene+Pipeline#USDintheScenePipeline-WhybuildUSDsupportintotheSceneBuilder,whynotcreateanewUSDbuilder?)
    *   We will use the official Pixar library, instead of building our own logic for parsing USD files: [https://github.com/PixarAnimationStudios/USD](https://github.com/PixarAnimationStudios/USD)
*   Update the scene builder to load USD files during Create Jobs, so asset references can be examined and resolved.  
    *   Emit references to resolvable USD files as source dependencies.
    *   Fail or error in the job if the reference can't be resolved.
        *   [Open question - failure versus error. This is a two way door and can be easily changed at any time.](https://wiki.agscollab.com/display/lmbr/USD+in+the+Scene+Pipeline#USDintheScenePipeline-Shouldwefailajob,oremitanerrorifareferencetoanotherUSDfilecan%27tberesolved?)
    *   Job dependencies won't yet be needed, because the flattening process will not need to reference product assets from other USD files.
*   Update the scene API to reference both Assimp and the USD library.
*   Update the scene API to detect USD format files, and branch to specialized USD loading.
    *   Make sure this allows for both a simple load that just scans for references, independent of the more advanced loading.
*   Write a custom asset path resolver, that we pass to the USD library, so it can resolve paths.
    *   See USD documentation on Asset Resolution:
        *   [https://graphics.pixar.com/usd/release/glossary.html#usdglossary-assetresolution](https://graphics.pixar.com/usd/release/glossary.html#usdglossary-assetresolution)
        *   [https://graphics.pixar.com/usd/release/api/ar\_page\_front.html](https://graphics.pixar.com/usd/release/api/ar_page_front.html)
*   In process job for a USD file in the Scene Builder, flatten the USD file using the USD library.
*   In process job for a USD file in the Scene Builder, convert the flattened USD file to an O3DE scene.

Open Questions
==============

Should we move Assimp to a Gem instead of using O3DE 3rdParty system?
---------------------------------------------------------------------

Moving forward with USD via Assimp will require regularly pulling new versions of Assimp as new features come online (example: references). Going through the 3rdParty library system is slower as it requires its own approval process via [O3DE's 3rd Party Package repo](https://github.com/o3de/3p-package-source). The downside to moving Assimp into a gem directly, is that we'd reimplement what's already in 3rdParty, for example, [git pulling a particular Assimp commit](https://github.com/o3de/3p-package-source/blob/main/package-system/assimp/build_config.json#L2-L3), [building](https://github.com/o3de/3p-package-source/blob/main/package-system/assimp/build_assimp_windows.cmd), and [packaging](https://github.com/o3de/3p-package-source/blob/main/package-system/assimp/install_assimp_windows.json) the library. This would also increase build time when compiling the editor source; Assimp takes 2 minutes to download and compile on my local 16-Core 3.4GHz PC.

Should we fail a job, or emit an error if a reference to another USD file can't be resolved?
--------------------------------------------------------------------------------------------

I'm leaning toward failure. This would be similar to a gap in a nested prefab hierarchy. We could try to preserve what we can from the file, but really we would want to call attention to the file with the broken reference to get it resolved.

Do we need to emit references to other USD files as job dependencies, or can they just be product dependencies?
---------------------------------------------------------------------------------------------------------------

If we're able to fully rely on procedural prefabs to manage the composition of USD file references, and we're able to predict the sub IDs of products from other USD files, then we may not need to emit these references as source dependencies.

USD has support for abstract base objects, that function sort of as inverted overrides. Our prefab system does not have a similar concept, so we may need to output job or source dependencies to use this information to build a prefab hierarchy. We may find that we instead need to add this sort of functionality to prefabs.

### Example

Assume tires.usda has a small\_tire prim and big\_tire prim, which results in the generation of small\_tire.procprefab and big\_tire.procprefab (plus the associated azmodels). Then, assume car.usda has four references to the small\_tire prim. When car.usda is processed, to create car.procprefab, the asset ID of small\_tire.procprefab will have to be resolved

Why not create a new USD builder upstream of the Scene Builder, and use intermediate assets as a middle step?
-------------------------------------------------------------------------------------------------------------

Would there be a situation that makes sense where we have USD builder with two collections of outputs? 1) Procedural prefabs to represent the relationship between USD files and 2) Flattened scene data in a format that we can load via Assimp (FBX, Collada, GLTF, etc) output as intermediate assets, to let the scene builder do the rest of the lifting from there?

Right now I'm leaning toward this being more complicated than it would be worth. It wouldn't save us much time, and the benefits (minimizing the code we change in the scene builder directly) aren't particularly high, especially because we'll still need to make changes to the scene API.

Can we put most of this logic into a Gem, instead of directly updating the scene API?
-------------------------------------------------------------------------------------

This would only apply if we decide on not using Assimp's USD implementation. It could establish a pattern for expanding to other, non-Assimp scene formats to load.

It may also be extra work that makes things more complicated, especially if USD ends up the only reference focused scene format we want to support to this degree, and we're OK with using Assimp or an equivalent for all other file types.

Do we need to update the scene API to support loading multiple scenes from the same file?
-----------------------------------------------------------------------------------------

The concept of a stage in a USD file can result in us wanting to generate multiple groupings of product assets, that function as if they came from different source scene files. For example, if we had tires.usda that had one group of prims that generated small\_tire.procprefab and associated other product assets (models, buffers, etc) and another group of prims that generated big\_tire.procprefab, would it be better to handle that separation and splitting in the current scene graph, giving the current scene pipeline one large scene? Or would it make more sense to, at some point in USD processing before we move to the O3DE scene in memory, to generate multiple O3DE scenes and run each of those through the current pipeline?

Right now I'm leaning toward that second option: We update the scene API, scene builder, etc to support processing multiple O3DE scenes from a single file.

How configurable can we make the prefab generation?
---------------------------------------------------

Right now, based on the relationship of USD files, it seems like we'll need to more heavily lock down the procedural prefabs generated from USD files, and not give as much configuration.

However, to facilitate technical artists expanding on things, we should still provide some entry points for appending custom components at steps, passing through information like user defined properties to facilitate that. We should also provide an interface to technical artists to generate additional procedural prefabs in response to the creation of our canonical USD generated procedural prefabs.

What do we need from procedural prefabs for USD to be fully functional?
-----------------------------------------------------------------------

This needs additional investigation, to see if the current state of procedural prefabs can meet our needs.

### New Entity Context?

Will we want to build a new context, and new entity classification for this process, "Scene Entity?" I don't think we will, but I'm putting this here as an open question for additional discussion.

### Abstract Prefabs

One feature of USD is abstract prims ([defined as Class in USD terminology](https://graphics.pixar.com/usd/release/glossary.html#class)).

Right now, procedural prefabs don't support the concept of references to incomplete prefabs.

Procedural prefabs also do not support the concept of an abstract prefab, that can't itself be instantiated.

What is the use case here? This allows for building a sort of inverted version of what we accomplish with prefab nesting right now. Instead of having a complex base prefab with simple prefabs that reference it (car.prefab, redcar.prefab), the base can be simple and the references can be complex.

USD files referencing non-USD file?
-----------------------------------

A USD file commonly references other file types like obj, fbx, and gltf.

If we're loading USDs with Assimp, we should be able to support referencing other file types supported by Assimp.  

Do all USD overrides work with prefabs, or will we have to special case some?
-----------------------------------------------------------------------------

The concern here is if some USD overrides modify properties that we can't support with prefabs. For example, can a USD override can modify the geometry of a mesh, or the length of an animation? That information is baked into those product assets, so in that case we would need to generate a new azmodel or animation and not be able to use the prefab reference system.

Would it make sense to do this in phases, flattening in the first part?
-----------------------------------------------------------------------

Converting USD information to prefabs correctly is going to take a lot of time. Would there be value in us doing a first deliverable where we flatten USD files in process jobs, and functionally treat them as other scene files from there?

The upside is that, from a content organization standpoint, we would still support the strengths of USD, the references between files. This work wouldn't prevent us from building the robust prefab solution, later.

When should we do this?
-----------------------

We may want to wait until more digital content creation tools support USD driven workflows. 

USD's biggest strength is the cross file referencing, letting content creators work collaboratively in ways they cannot achieve with traditional scene file formats. However, right now, full USD support is largely just Pixar's digital content creation tool and NVidia's _USD Composer_. Max, Maya, and Blender all push content creators to a workflow of treating USD as an export format, and not the primary authoring format. This is a gotcha because the primary authoring format for those files (.blend, .ma, .mb, and .max) do not support USD file references.

This means that, even if we had full USD support today as proposed in this document (generating prefabs that match the USD relationship graph), it's unlikely that many of O3DE's customers would be able to make use of USD support.

Out of Scope / Long Term Work
=============================

Advanced shaders and materials
------------------------------
For our initial deliverable, we will generate basic O3DE physically based rendering materials.

However, the options for shaders and materials from USD files are much bigger than that. As we wrap up our first set of work to support USD, we'll want to start planning what a more fully featured material and shader pipeline from USD files could look like.

Extra USD Properties and O3DE Components
------------------------------
Allow developers to read generic scene information, and inject custom components into the procedural prefabs generated by importing USD files. O3DE would ship with built-in components. 
1.  Update Assimp to output USD property data
        1.  Assimp::Scene root "kitchen"
            1.  child 01
                1.  Name: "spoon 01"
                2.  Transform: <translation>, <rotation>, <scale>
                3.  Reference: "c:/project/spoon.usd"
                4.  Variant: "WoodenSpoonA"
                5.  NEW: Other user data/properties/overrides

1.  O3DE scene system already supports storing generic info into each scene node

Editor Variant Selection Dropdown 
------------------------------
Allow designers to select from a list of available variants from within the editor when a variant set is included in a USD

Convert O3DE prefabs to USD files
---------------------------------

This initial work would not include the ability to create USD files from O3DE content, like prefabs. A prefab to USD converter could be the first step towards replacing prefabs with USD entirely. 

This is something that could be looked at long term.

FAQ
===

Why not stop after phase 2: just treat USD like other formats, flatten everything, and then generate product assets?
--------------------------------------------------------------------------------------------------------------------

By expanding USD support to create a prefab hierarchy that mimics the USD relationship graph, we will see two main areas of benefit:

*   Performance
    *   Speed - Asset processing time will improve for USD files, because we won't have to re-flatten the hierarchy for every USD file processed.
    *   Hard drive space used - This will reduce the duplication of geometry in product assets on disk, because procedural prefabs will allow output to reference already created product assets instead of flatting and re-creating that geometry in multiple product assets.
    *   Memory usage - Flattening each USD file will result in needing to keep the entire USD file in memory. This will put USD in the same situation as FBX, GLTF, etc, of having an upper limit on what can be processed due to memory available on the local machine. Switching to a prefab focused output mimicking the relationship graph means that in many cases, we will be able to avoid loading dependencies in memory, because we will be able to generate a prefab without loading that other geometry.
*   Workflow
    *   More prefabs output will give content creators in the Editor more options for assembling content in the Editor from scene files.

Why do we need both source dependencies and job dependencies for references?
----------------------------------------------------------------------------

*   Source dependencies are necessary because, whenever a referenced USD file changes, the USD file with that reference will need to be re-processed, because those changes to the reference could result in needing to update the product assets for the current file.
*   Job dependencies are necessary because some references to other USD files will rely on the product assets of the referenced file.
    *   Example: If cityblock.usda has an instance of the root prim scene for car.usda placed in it, then cityblock.procprefab will have an instance reference of car.procprefab. To process cityblock.usda and create cityblock.procprefab, the asset ID of car.procprefab will have to be known.

Why build USD support into the Scene Builder, why not create a new USD builder?
-------------------------------------------------------------------------------

*   We will want a lot of the processing steps in the scene builder to be available for the USD building process: Scene manifests to help control scene settings (although these may be full of unique options for USD files to control composition), Python callbacks at appropriate steps. Product asset generation for USD files will include many of the same output types that other scene files generate: models, materials, buffers, etc.

Why use an existing library for loading USD files, instead of rolling our own solution?
---------------------------------------------------------------------------------------

USD is a complex format, it will take us much longer to write our own logic for loading these files than it would take to use the existing solution. We also will have a lot more bugs to work through, writing our own solution.

Are there other reasons for customers to want to use USD over our existing, supported scene file formats?
---------------------------------------------------------------------------------------------------------

### FBX versus USD

*   FBX is proprietary, so support from non-Autodesk tools is often mixed, including our own.
    *   FBX being proprietary also makes upgrading to new FBX formats difficult, because there is no spec to build against, just reverse engineering options.
*   FBX does not fully support some features we want, like physically based rendering materials.
    *   Our Maya support is built around a Maya specific extension to FBX. Blender doesn't even attempt to support PBR material properties in FBX files.
*   FBX does not support anything similar to the variant management and compositing that USD supports, which is the primary strength of USD.

### FBX versus GLTF

*   GLTF is built for web and runtime usage. GLTF strips data that isn't necessary to minimize file size, which can reduce its usefulness in a complex pipeline.
*   GLTF does have some support for instancing and referencing other content, but it's not as robust as USD's, it does not support variant management to the same degree as USD.

Would we drop support for other scene file formats with USD support? Why wouldn't we want to only support USD?
--------------------------------------------------------------------------------------------------------------

To get the most out of USD would require studios to go all in on it, and to have a need to break up content in that way.

For many studios, FBX, GLTF, Collada, etc are all good enough. They have existing workflows that work well with these scene file formats, and/or they have libraries of content already built in these formats.

A large, reference based USD library may require content creators to keep their working assets in USD format, and not really able to use Maya, Max, or Blender native formats. This could be a huge workflow adjustment for some studios that they aren't interested in making.

USD support would be in addition to other scene file formats, and not a replacement.

Are there other benefits to supporting USD this way?
----------------------------------------------------

Right now we have some big challenges with importing very large scenes. We load the entire scene into memory twice at the same time (Assimp + O3DE).

Our current pipeline for the most part works best when you give it large, composited scene files. We have no processing tools that can reconstruct a large complex scene from multiple different source scene files.

USD support would give us that. If a customer had a complex setup that would result in an extremely large single FBX file, they may be able to instead manage that content as multiple, smaller USD files that reference each other to create the same scene. This seems to be a big driving factor in Pixar creating this format to begin with, they were trying to solve these scale problems.
