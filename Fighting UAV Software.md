This page describes the technical challenges of the competition in detail and explains the solutions developed during 2024 UAV Competition.
This page requires you to understand the [competition specification](https://teknofest.org/en/competitions/competition/33) deeply. Please read it several times before proceeding. Also reading [other teams](https://teknofest.org/tr/competitions/competition/33) and [our](https://cdn.t3kys.com/media/upload/user_form_upload/2024/5/6/4HK8mrNEZQBQYKWHI8aGJNm0HfN0GQQu.pdf) critical design report(CDR) highly recommended.
# Problem Description
Pamuk is a UAV design to compete in Teknofest Fighting UAV Competition. It has two primary goals, [[#Fighting UAV]] and [[#Kamikaze]].
## Fighting UAV
We divide this mission into two tasks called Stage 1 and Stage 2. Stage 1 covers everything from the start of the mission until the sighting of the desired UAV. Stage 2 starts when desired UAV sighted on camera and ends when a successful lock acquired(aka. mission is completed).
### Stage 1
This stage mainly consist of path planning and path tracking. A path is a curve or discrete points in 3D space that lead to the enemy starting from UAV's current position. Planned path should consider no flight zones and the dynamics of the vehicle. Planning stage should be fast as possible to reduce any problems caused by delay between planning and tracking. After the path is planned on the ground station it is sent to the UAV for tracking. There is no api that has the capability of tracking 
### Stage 2
This stage can use the onboard camera to better localize the enemy. Using this, one can track the enemy more accurately and reliably. Data coming from the referee server is inadequate for doing tracking reliably. When data acquired from the server, it can already be several seconds old. This means the enemy plane probably traveled more than 30 meters. This problem also amplified by the fact that received data's timestamp probably wrong because other teams usually do not sync their system clock exactly.
# Kamikaze
Kamikaze mission is relatively easier compared to Fighting UAV mission. A path can be planned at a fixed altitude to the QR code. After desired position reached, a custom algorithm can be used to control the pitch of the plane so it always faces the QR code. After reaching an altitude limit, nose will pitch up and plane will recover from the dive. Exact values for this operation can be tested and approximated on [[#rotasim]].
# Motion Planning
[Motion planning](https://en.wikipedia.org/wiki/Motion_planning)  needed almost every step of the problem to answer questions like:
 - What to do when idling?
 - How to get from point A to point B?
 - What is the shortest and most efficient route to some position and heading in 3D space?
This problem is well researched in robotics and has several different solutions proposed over the years but most notable ones are:
 - [Hybrid A*](https://medium.com/@junbs95/gentle-introduction-to-hybrid-a-star-9ce93c0d7869)
 - [Dubins path](https://en.wikipedia.org/wiki/Dubins_path) based [RRT*](https://en.wikipedia.org/wiki/Dubins_path)
There are several motion planning frameworks available that has implementations for many algorithms:
 - [OMPL](https://ompl.kavrakilab.org/planners.html)
 - [NAV2](https://docs.nav2.org)
These algorithms are designed mainly for surface robots with actuators like wheels and move on 2D space. However UAVs also have a need to change altitude. This complicates many things while planning a route. UAV can simply loiter and change its altitude, then do all the planning in 2D space but this has obvious disadvantages. Second option is to plan with the third dimension in mind but this makes the problem harder to solve and very little research made about this approach compared to 2D variants.

For 2024 competition, we developed an [RRT*]( https://en.wikipedia.org/wiki/Dubins_path) implementation that uses [Dubins paths](https://en.wikipedia.org/wiki/Dubins_path) to do global planning. Its written in C++ without using any frameworks other than a library named [nanoflann](https://github.com/jlblancoc/nanoflann) to handle [nearest neighbor search](https://en.wikipedia.org/wiki/Nearest_neighbor_search). You can view its source code [here](https://github.com/sezeriper/planner/blob/main/src/gcs/path_planner.hpp).
# Computer Vision
Computer vision tasks includes detecting and tracking the enemy UAV from the image acquired from onboard camera. This is usually done with a [object detection](https://paperswithcode.com/task/object-detection) model trained with a custom dataset and a [SOT](https://arxiv.org/pdf/2201.13066) model for tracking the detected bounding box through the frames more accurately. The success of this method depends on the quality of the dataset that used to train the object detection model.

For 2024, we tested several YOLO models and achieved best results with [YOLOv10](https://github.com/THU-MIG/yolov10.git) but these tests are made without using any kind of methodology so they are purely hypothetical. In combination with YOLOv10 a single object tracker named [DaSiamRPN](https://docs.opencv.org/4.x/de/d93/classcv_1_1TrackerDaSiamRPN.html) used essentially to fill in the frames with low detection confidence by running every frame. We considered this system good enough by testing it on unseen videos of UAVs and RC planes.

This technique uses only the data from a single frame of the camera. It can probably be improved by using [information from previous frames](https://docs.opencv.org/3.4/d4/dee/tutorial_optical_flow.html) for object detection. It can also utilize the estimated position of the enemy coming from [[#GCS]] to increase its confidence. Further research needed.
# rotasim
[rotasim](https://github.com/sezeriper/rotasim.git) is a simulation for fixed wings developed for 2024 competition to test developed algorithms and different mission scenarios. It uses the open source game engine [Godot](https://godotengine.org/) with a custom plugin that integrates [JSBSim](https://github.com/JSBSim-Team/jsbsim.git) to simulate flight dynamics. Advantage of using a common 3D game engine is you don't need to worry about things like high quality 3D rendering, environment design and networking because those are already refined after years of development and community feedback. [JSBSim](https://github.com/JSBSim-Team/jsbsim.git) is flight dynamics model(FDM) that can run in real time and can give very true to life results if used correctly. Its integrated into the game engine in a modular fashion so it can be used like any other component of the engine. There is also a component that implements [MAVLink](https://mavlink.io/en/) protocol to communicate with [SITL](https://docs.px4.io/main/en/simulation/#sitl-simulation-environment) [autopilots](https://docs.px4.io/main/en/). Feature set can be extended to include support for [ROS](https://www.ros.org/) interface, virtual cameras, multiple agents and referee server.
# GCS
Ground control station([GCS](https://github.com/sezeriper/planner/tree/main/src/gcs)) is a user interface made for the specific requirements of the missions. It must be able to control the specific parameters of the UAV. This includes starting or stopping the current mission, selecting an enemy UAV and visualizing the flight zone, obstacles, other UAVs etc. This can be achieved using many kinds of programming languages and frameworks. One option is to make it web based. This option brings the capabilities of HTML and CSS which is the go-to standard for making any kind of UI these days but has the added complexity of a backend architecture. Another option is to use Python which is a good choice because of its compatibility with [MAVLink](https://mavlink.io/en/) and [ROS](https://www.ros.org/) but has poor 3D graphics support.

For 2024 year, C++ is chosen to build the [[#GCS]]. Many of the necessary like libraries [MAVSDK](https://mavsdk.mavlink.io/main/en/index.html)are written in C++. This means C++ has first class support. It also has very good 3D graphics capabilities because of the game industry and good UI libraries like [ImGui](https://github.com/ocornut/imgui.git).
# Middleware
All of the components of the system needs to communicate with one another. For example path planner needs to send its path to UAV for execution. UAV needs to send its mission status periodically to report if its successful or not. In 2024 year, we used [gRPC](https://grpc.io/) to achieve this. gRPC is selected because it's cross platform, used widely in industry and has good C++ support. However it lacks [publish-subscribe](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) pattern and is way too low level for robotics usage so it's hard to maintain. Because of this, switching to ROS 2 is considered for the following reasons:
 - Can communicate between multiple components without a server-client architecture.
 - Specifically made for robotic usage.
 - It has many tools for visualization, testing and simulation.
 - Has many frameworks for tasks like path planning.
 - Has tight integration with PX4.
The downside is you need to install a specific version of [Ubuntu](https://ubuntu.com/) and packages. Also it is more than a simple middleware so it has a steep learning curve.
# [PX4](https://docs.px4.io/main/en/)
[PX4](https://docs.px4.io/main/en/) is the autopilot software that runs in the [flight controller](https://docs.px4.io/main/en/flight_controller/cubepilot_cube_orange.html#cubepilot-cube-orange-flight-controller) itself. It control all the actuators, stabilizes the plane, gathers sensor information and has many different APIs. It has improving ROS support so we find it more suitable than Ardupilot for robotics which is another widely used autopilot. Understanding PX4 is crucial for developing software for UAVs because all of the work is done essentially for just sending control commands to PX4. Here are some relevant links to offboard control:
 - https://docs.px4.io/main/en/robotics/
 - https://docs.px4.io/main/en/ros2/
 - https://docs.px4.io/main/en/robotics/mavsdk.html
 - https://docs.px4.io/main/en/ros/offboard_control.html
General knowledge about PX4 highly recommended.
# Dev Environment
Recommended operating system is Ubuntu 22.04 because of PX4s recommendations. Although PX4 can be compiled in other Linux distributions like Fedora but ROS also recommends Ubuntu for its environment. Here are the recommended environment:
 - [Ubuntu 22.04](https://releases.ubuntu.com/jammy/)
 - [ROS 2](https://docs.ros.org/en/iron/Installation/Ubuntu-Install-Debs.html)
 - [PX4](https://docs.px4.io/main/en/dev_setup/dev_env_linux_ubuntu.html)
 - [Visual Studio Code](https://code.visualstudio.com/)