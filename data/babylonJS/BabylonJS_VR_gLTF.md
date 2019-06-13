# Babylon.js

[Babylon.js](https://www.babylonjs.com/) is a complete JavaScript framework and graphics engine for building 3D applications with HTML5 and [WebGL](https://www.khronos.org/webgl/). It works on any browser that supports WebGL. It is possible to load and draw 3D objects, manage these 3D objects, create and manage special effects, play and manage spatialized sounds, create gameplays and more. 3D scenes can be set up with minimum lines of code. 

## Getting Started

First, check if your browser is WebGL compatible by visiting [WebGL.org](https://get.webgl.org).

An HTML file defining a 3D scene with Babylon.js can be easily developed and consists of the following parts:
* "head" defining settings and JavaScript files to be imported as resources (e.g. https://preview.babylonjs.com/babylon.js)
* "body" containing a canvas element and a script. The script is written in JavaScript and enables to create and populate the 3D scene

The script section contains the key definitions as in the example here below.

```JavaScript
    // Create canvas and engine
    var canvas = document.getElementById("renderCanvas");
    var engine = new BABYLON.Engine(canvas, true);

    //Call the createScene function
    var scene = createScene(); 

    // Register a render loop to repeatedly render the scene
    engine.runRenderLoop(function () {
        scene.render();
    });

    // Watch for browser/canvas resize events
    window.addEventListener("resize", function () {
        engine.resize();
    });    
```
The definition of `createScene()` function can be customized to create scenes as complex as needed in terms of cameras, lights, and 3D objects.

The rendering engine will take care of continuously rendering the scene in a loop and resizing it if the browser window is modified.

[BabylonJS_VR_gLTF.html](BabylonJS_VR_gLTF.html) is an example showing how a 3D scene can be created and loaded on a web browser.

## Create 3D scene

The `createScene()` function starts with creating the scene variable:

 ```JavaScript
    var scene = new BABYLON.Scene(engine);
 ```

By default babylon.js adopts a left-handed system, whereas most CAD systems adopts a right-handed system (see the figure below for the difference). 

 ![coordinate system](https://docs.microsoft.com/en-us/windows/desktop/direct3d9/images/leftrght.png)
 
 This problem can be solved by forcing the adoption of a right-handed system:
 
 ```JavaScript
 scene.useRightHandedSystem = true;
 ```

Moreover, it must be noted that babylon.js adopts the Y-up convention (i.e. y-axis is the vertical axis). Therefore, appropriate conversions are needed if source data (i.e. 3D models or placements) are defined with Z-up convention. The following rules can be applied if a Z-up placement (x,y,z) with Euler angles (rotX,rotY,rotZ) is converted to a Y-up placement (x',y',z',rotX',rotY',rotZ'):

    x' = x
    y' = z 
    z' = -y
    rotX' = rotX - Math.PI/2
    rotY' = rotY
    rotZ' = rotZ

### Setting up cameras and lights

A camera is needed to get a view in the scene. There are several types of cameras Babylon.js framework provides. Two most used are universal camera and arc rotate camera:
- *Universal(free) camera* is used for first person movement like control. It is more natural one and shows the scene like we perceive the world.
-	*Arc rotate camera* always points towards a given target position and can be rotated around that target with the target as the center of rotation. 

All these cameras can be created in the scene with a minimum number of lines of code. The constructor needs a name, position for the camera, and scene reference.
In the example a free camera was chosen since it gives the most natural view. Using the arrow keys on keyboard, the camera can be moved around the scene. Holding the left mouse button and moving the mouse, the viewpoint of the camera can be controlled. Moreover, the initial position of the camera can be set based on the contents of the scene.

```sh
    var camera = new BABYLON.ArcRotateCamera("Camera", Math.PI / 2, Math.PI/2, 2, new BABYLON.Vector3(0,0,5), scene);
    camera.attachControl(canvas, true);    
```
Similarly, also sources of light can be defined and added to the scene:

```JavaScript
    var light1 = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(1, 1, 0), scene);
    var light2 = new BABYLON.PointLight("light2", new BABYLON.Vector3(0, 1,-1), scene);
```


### Importing 3D objects to the scene

3D objects can be dynamically added to the scene. Just a few line of codes are needed to customize the HTML file.

Babylon.js works with different file formats of 3D models. The built in file type is .babylon which uses JSON file format and Babylon.js can load these without a plugin. Moreover, formats like Obj, glTF and STL are supported. Importing models from many popular 3D modeling tools such as Blender or 3DS Max is possible as well.

In the example, 3D Models are imported in gLTF file format. glTF standard offers .bin and the texture files to be combined into a single file in the form of .glb extension. In this way, we will deal with a single file while avoiding to explicitly handle materials and textures.

![gltf](https://www.khronos.org/assets/uploads/apis/2017-gltf-20-launch-2.jpg)

Aiming at semplifying the creation of the scene, the `import_OBJ` function has been developed to deal with importing 3D objects and placing them in the scene while specifing position, scaling, and rotation:

```JavaScript
/**
* Import an object to a 3D scene.
* @param  {BABYLON.Scene} scene The scene where the object is imported
* @param  {string} objID Identifier of the object to be imported
* @param  {string} filepath Definition of the gLTF file to be loaded in terms of URL ("http://www.example.com/example.glb") or local filepath (e.g. "models/example.glb")
* @param  {Number} pos_x Position of the object along the x-axis. 
* @param  {Number} pos_y Position of the object along the y-axis
* @param  {Number} pos_z Position of the object along the z-axis
* @param  {Number} scal_x Scaling of the object along the x-axis (1 means no change)
* @param  {Number} scal_y Scaling of the object along the y-axis (1 means no change)
* @param  {Number} scal_z Scaling of the object along the z-axis (1 means no change)
* @param  {Number} rot_x Rotation of the object about the x-axis in terms of radians
* @param  {Number} rot_y Rotation of the object about the y-axis in terms of radians
* @param  {Number} rot_z Rotation of the object about the z-axis in terms of radians
*/	
function import_OBJ (scene, objID, filepath, pos_x, pos_y, pos_z, scal_x, scal_y, scal_z, rot_x, rot_y, rot_z)
```

In must be noted that the unit of measurement is not explicitly set in BabylonJS. Therefore the user must implicitly consider a reference unit (e.g. 1 unit = 1 m) and then adjust all positions and scaling accordingly. Regarding imported 3D objects, particular attention must be paid to the unit of measurement used to generate the source 3D model.

When a 3D model is imported, all meshes are grouped under a node named according to the object ID. 

`import_OBJ` function can be called as many times as needed inside the `createScene()` function to customize the 3D scene, e.g. if the 3D model is available online:

```JavaScript
    import_OBJ(scene, "http://www.example.com/avocado", "https://cdn.jsdelivr.net/gh/KhronosGroup/glTF-Sample-Models/2.0/Avocado/glTF-Binary/Avocado.glb", 8, 0, -9, 10, 10, 10, -Math.PI*0.5, 0, 0 );
```
or the 3D model is stored in a local folder:

```JavaScript
    import_OBJ(scene, "gltf_models/Avocado.glb", 8, 0, -9, 10, 10, 10, -Math.PI*0.5, 0, 0 );
```
In this way it is possible to dynamically reconfigure the HTML file by changing just a few lines in a well-defined section of the file.
 

## Inspector

The proposed example exploits also the Inspector that provides debugging functionalities:
-	A hierarchical view of the scene
-	Multiple property grids to let dynamically change object properties
-	Specific helpers like the skeleton viewer, etc.

The Inspector interface consists of two panes on the left and right side of the scene:
-	The scene explorer pane (left)
-	The inspector pane (right)

![inspector](https://cdn-images-1.medium.com/max/2600/1*DWKIaNvmYcBnvYvekzvouA.png)

The scene explorer displays a hierarchical view of the scene including nodes, materials and textures. In Babylon.js, each mesh, light, and camera extends the Node class. You can see all the 3D Models imported in this panel with their parent-child relationships and enable/disable them. 

The inspector pane contains 4 tabs:
-	The property grid which will display configurable controls for the current selected entity. You can change here the transformation values of the selected object
-	The debug pane that allows to turn features on and off. 
-	The statistics pane gives information about all metrics captured by the engine
-	The tools pane lets access utilities (like screen capture or recording as well as several tools related to glTF)


## References

* Babylon 101- First Steps, https://doc.babylonjs.com/babylon101/first. 
* Resources- Using External Assets, https://doc.babylonjs.com/resources/external_pg_assets
* Load files with Assets Manager, https://doc.babylonjs.com/how_to/how_to_use_assetsmanager.
* glTF overview, https://www.khronos.org/gltf/. 
* The Inspector Overview, https://doc.babylonjs.com/features/playground_debuglayer.
