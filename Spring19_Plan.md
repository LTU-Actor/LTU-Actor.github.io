# ACTor Spring 2019 Plan

- **LTU Faculty Co-Advisor**: CJ Chung <cchung@ltu.edu>
- **LTU Faculty Co-Advisor**: Nick Paul <npaul@ltu.edu>
- Mitchell Pleune <mpleune@ltu.edu>
- Sean Bleicher <sbleicher@ltu.edu>
- Charles Faulkner <cfaulkner@ltu.edu>

## Initial Steps & Dates

 1. Test current code (from Dec 2018) on new computers
 2. Present current software for RTK status update (early Feb.)
 3. Finalize wiring (remove seats for better access)
 4. Make poster for LTU Research Day (April 5)

## Meeting Times

- Leader meeting 8:30pm Wednesdays
- 3:00pm ~ 4:00pm Fridays

## System Diagram

![][sysdiag]

#### Notes

- RTK is a very large section, shown as a disproportionally small box
- All packages will target ROS Kinetic on Ubuntu 16.04 LTS
- Image processing nodes will use OpenCV 3 (default as of Kinetic)
- Route system will use Lua5.2
- Nodes in orange are marked for generalization. These packages will be put in
  their own git repository in a new GitHub organization named LTU-Actor.
- The route input nodes will be unaware of the eStop button state.
- The eStop node should latch, and must be explicitly cleared by the route
  system (possible through the web interface).
- The route system will be way-point based, and will take a list of way-points
  from a file. The route system will mark way points as done once it has both
  gotten within a minimum distance, and when that distance has reached a local
  minima.
- The sections of restricted code in the route system should be fully modular
  and not stored in the GitHub repository. All RTK additions to the route
  system could be distributed in a single git patch file.
- The web page will use [`Next.js`](https://nextjs.org/) and will communicate
  with ROS with [`roslibjs`](https://github.com/RobotWebTools/roslibjs), and
  will have access to all video topics with
  [`web_video_server`](https://github.com/RobotWebTools/web_video_server).
- [Adaptable-Parameter-Server](https://github.com/LTU-Actor/Adaptable-Parameter-Server)
  will be best suited for input processing nodes, since it will normalize any
  changes in lighting, etc.
- The vehicle platforms may want to implement a service to generate a spline
  that estimates the path the vehicle will take with the current inputs.
- Vehicle platform packages contain launch scripts to run the system. If the
  platform has multiple computers, then the configuration of which node to run
  on which computer will be in the package as well.

## Semester Goals

- Crate modular input, processing, and route nodes
- Reimplement some route input nodes with neural networks
- Rewrite route system
  - Route files will be Lua scripts
  - Modular interface with RTK navigation stack, not uploaded to GitHub
  - Way point coordinator to track which way-point to go to next
    - Routes are not required to have or use way-points
  - Use ROS parameters to define system constants like speed multiplier
- Update vehicle platforms
  - Dataspeed Polaris GEM
  - LTU L2Bot
  - EduBot
- Create node that manages dynamic reconfigure parameters (**Charles**)
  - Periodically save entire name-space
  - Automatically apply saved changes once all nodes have started
  - When nodes are launched late, apply their options
- Create more robust eStop node (**Mitch**)
- Adapt web page to new system (**Mitch**)
- Remove latency from GEM subsystem

## Actor Package List

| Name    | Packages Owned |
| ------- | -------------- |
| Mitch   | 5              |
| Charles | 7              |
| Sean    | 6              |

| Package                     | Owner   | Notes                                |
| --------------------------- | ------- | ------------------------------------ |
| Core                        | Mitch   | Route, web-page, sub-repo packages   |
| eStop                       | Mitch   |                                      |
| AdapParameter               | Mitch   |                                      |
| Sensor-AutolivRadar         | Sean    |                                      |
| InputProcess-CamAdjust      | Charles |                                      |
| InputProcess-Canny          | Mitch   | May be included in AdapParameter     |
| InputProcess-Threshold      | Mitch   | May be included in AdapParameter     |
| Route-Blob                  | Charles |                                      |
| Route-Joystick              | Sean    |                                      |
| Route-StopSign              | Charles |                                      |
| Route-GPS                   | Sean    |                                      |
| Route-Pothole               | Charles |                                      |
| Route-Parking               | Charles |                                      |
| Route-Obstacle              | Sean    |                                      |
| Route-StopLine              | Charles |                                      |
| Route-OneWay                | Charles |                                      |
| Route-NeuralDetect          | Sean    | Neural network sign & object detect  |
| Vehicle-GEM                 | ALL     |                                      |
| Vehicle-L2Bot               | ALL     |                                      |
| Vehicle-EduBot              | Sean    |                                      |

If all goes as expected, then these are the packages that will be in the
[LTU-Actor GitHub](https://github.com/LTU-Actor). To clone the system onto a
computer, all that will need to be done is a recursive clone of Core.

## Package Guidelines

- Use [clang-format](https://clang.llvm.org/docs/ClangFormat.html).
- Configure nodes with ROS parameters to set options that don't need to be
  tweaked like subscribed topic names.
- Use dynamic reconfigure.
- Store documentation with the repository files. Upload images (and other
  binary assets) with [git LFS](https://git-lfs.github.com/).
  - Create documentation in markdown when possible so changes can be tracked.
  - Keep the README short and simple, with a couple pictures that help explain
    the node.
  - List input topics (and parameters to configure them), and output topics in
    the README.
- Keep all ROS dependencies updated so [`rosdep`](http://wiki.ros.org/rosdep)
  works.
- Prefix GitHub package names with their subsystem.
  - `Sensor-` for sensor nodes
  - `InputProcess-` for input processing nodes
  - `Route-` for route input nodes
  - `Vehicle-` for robot platforms
- Each package should be owned by one person who does most of the work and
  documentation.
- Nodes should check if they have subscribers, and only do work when necessary.

[sysdiag]: https://media.githubusercontent.com/media/LTU-Actor/LTU-Actor.github.io/fa3a36328e18626678b23ffdcba7c4e239e370fc/assets/system_diagram.svg
