# RFC Feature - Console Only Game Launchers for Simulations

### Summary

This RFC proposes introducing the ability to the game launcher to launch in console mode only without the creation of a native window. Currently, the only launchers that can do this are the server and headless server launchers. The feature to support console-only game launchers is useful for simulation use cases since the native client windows is typically used just to provide visualization of the scene and is not used directly by any ROS2 sensor.

### What is the relevance of this feature?

The general use case for not creating a native client window when launching the game launcher is primarily useful for simulation scenarios. This has little or no value for gaming scenarios. Currently for simulation projects, the client window that is created provides a view based on a camera that is configured in the simulation scene being launched. The camera may or may not be tied directly to the simulation target (ie robot), and may just provide a view of the scene to provide photo-realistic view of the simulation in general. (In many cases, the camera view can be changed using the same mechanisms as games do).

There are scenarios where the client window is not needed all, mostly in cases where automation or CI/CD pipelines are used to validate robot code. Furthermore, the O3DE Atom renderer may or not be needed for simulation use cases that do not O3DE's high-quality visualizations at all and thus needing to only rely on the simulations Physics and non-camera ROS2 sensors. The renderer will be needed for scenarios where there are sensors that utilize the ROS2 camera sensor even if the client window is not necessary.

### Feature design description

Window-less clients will still launch with rendering capabilities enabled, but will not launch any native client window to show the game/simulation scene based on its camera view. The renderer will still be available to support features like the ROS2 Gem's camera sensor implementation. The underlying RHI will be initialized based on the current platform and hardware (DX12, Vulkan, etc), which means that it will still require video and graphics drivers to be available. 

The control of creating the client window or not will be determined in one of the following ways:

* Through the use of the [settings registry](https://www.docs.o3de.org/docs/user-guide/settings/), controlled by the key `/O3DE/Launcher/Bootstrap/ConsoleMode`.
* Through a command-line argument `-console-mode`
* If the null renderer is specified through the command line arguments (`-rhi=null`)

#### Examples

##### Launching GameLauncher with Native Windows creation

**Robot Manipulator**

https://github.com/o3de/sig-simulation/assets/82231385/c10732da-cfd6-4b44-8042-9efa41309ba9

**Robot Vacuum**

https://github.com/o3de/sig-simulation/assets/82231385/6c4cdefb-06ef-406e-89e2-d117dafdadfa

##### Launching GameLauncher without Native Windows

**Robot Manipulator**

https://github.com/o3de/sig-simulation/assets/82231385/5a469a33-5290-4bd0-adfd-445c3efaceba

**Robot Vacuum**

https://github.com/o3de/sig-simulation/assets/82231385/ad6cd9f3-aa9a-4787-80d9-b730c053ec84

### Technical design description

#### Client window creation control

##### Settings Registry
The control to create the client window in the client launcher will be controlled by the following settings registry key

```
/O3DE/Atom/Bootstrap/CreateNativeWindow
```

This key can be applied on a per-project case by providing a `bootstrap.setreg` file in the project's `Registry` subfolder. For example:

```json
{
  "O3DE": {
    "Atom": {
      "Bootstrap": {
        "CreateNativeWindow": false
      }
    }
  }
}
```
The default behavior of `CreateNativeWindow` will be `true`.

##### Atom Bootstrap

The creation of the native client window is triggered in the [BootstrapSystemComponent.cpp](https://github.com/o3de/o3de/blob/454b5625e55059955a0f35d84d62101e754bf7fd/Gems/Atom/Bootstrap/Code/Source/BootstrapSystemComponent.cpp#L327-L350) in the Atom gem. In addition to the check for the `appType`'s `IsHeadless()` attribute, the component will also check for one of the three conditions specified above to determine if the native window is created or not.


### What are the advantages of the feature?

* Allows simulation clients to run on a non-graphics intensive environment (headless)
* Allows simulation clients to run on headless, server only operating systems
* Provides option to not show a client window on the launcher when not needed
* Disabling the main render pipeline that is used by the client will reduce the memory and resources used by the simulations that employs rendering from the ROS2 camera sensor.

### What are the disadvantages of the feature?

* On Linux, the X11 and graphical drivers (i.e. Vulkan display driver, etc) will still be a system pre-requisite. This is due to the library dependencies on the XCB/X11 on this platform, even if the null renderer is set.
* The ability to set to console-mode at runtime is only supported in Linux. For Windows, launching in GUI mode vs console is a compile-time setting. Headless mode for game launchers is only possible with additional updates similar to the headless-mode for server launchers.

### How will this be implemented or integrated into the O3DE environment?

The changes needed will be backwards compatible with the current version of O3DE. 

* By default, if the `/O3DE/Atom/Bootstrap/CreateNativeWindow` is not set in the settings registry, the default behavior will create the client window for client launchers. 

### Are there any alternatives to this feature?

This is partially supported currently by setting the `rhi` argument to `null` that can be passed into the launcher. However, this does not prevent the creation of a client window, it only disables the loading and initialization of the backend Atom RHI.

### How will users learn this feature?

This will be added to the o3de documentation, specifically the for the simulation and ROS2 specific topics.

### Are there any open questions?

Q: Why have both headless mode and console mode? Aren't they the same?
A: Headless mode is intended for strictly non-graphical scenarios where the launcher can run on something like an SSH client and on a headless linux environment. Console mode may or may not require graphics (with the `-rhi=null` argument), but can still possess rendering capabilities that is performed outside of a native window UI that is present. 
