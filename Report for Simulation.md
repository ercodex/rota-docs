### Simulation and Simulators
Simulations is the primary test method for all of the developed algorithms. It’s used to model the missions from the actual competition so we can verify our system without the need of a real flight test.

Simulators allow PX4 flight code to control a computer modelled vehicle in a simulated "world". You can interact with this vehicle just as you might with a real vehicle, using QGroundControl, an offboard API, or a radio controller/gamepad.

> [!tip] TIP
> Simulation is a quick, easy, and most importantly, _safe_ way to test changes to PX4 code before attempting to fly in the real world. It is also a good way to start flying with PX4 when you haven't yet got a vehicle to experiment with.

PX4 supports both SITL (Software In the Loop) and HITL (Hardware In the Loop) simulations.
SITL is where the flight stack runs on computer (either the same or another computer).
HITL is where PX4 firmware is run on real flight controller hardware.

All simulators except for Gazebo communicate with PX4 using the Simulator MAVLink API. This API defines a set of MAVLink messages that supply sensor data from the simulated world to PX4 and return motor and actuator values from the flight code that will be applied to the simulated vehicle. The image below shows the message flow.
![[Pasted image 20241122213050.png]]
### What simulation software can be use for fixed-wing aircrafts with PX4?
Gazebo and Gazebo Classic are highly recommended simulators.
##### [Gazebo](https://docs.px4.io/main/en/sim_gazebo_gz/) 
Gazebo is more advanced version of Gazebo Classic. It is the only version of Gazebo available from Ubuntu Linux 22.04.  
Gazebo is a powerful 3D simulation environment that is particularly suitable for testing object-avoidance and computer vision.  It can also be used for [multi-vehicle simulation](https://docs.px4.io/main/en/simulation/multi-vehicle-simulation.html) and is commonly used with [ROS](https://docs.px4.io/main/en/simulation/ros_interface.html), a collection of tools for automating vehicle control.
**Supported Vehicles:** Quad, Standard VTOL, Plane

##### [Gazebo Classic](https://docs.px4.io/main/en/sim_gazebo_classic/)
A powerful 3D simulation environment that is particularly suitable for testing object-avoidance and computer vision. It can also be used for [multi-vehicle simulation](https://docs.px4.io/main/en/simulation/multi-vehicle-simulation.html) and is commonly used with [ROS](https://docs.px4.io/main/en/simulation/ros_interface.html), a collection of tools for automating vehicle control.
**Supported Vehicles:** Quad ([Iris](https://docs.px4.io/main/en/airframes/airframe_reference.html#copter_quadrotor_x_generic_quadcopter), Hex (Typhoon H480), [Generic Standard VTOL (QuadPlane)](https://docs.px4.io/main/en/airframes/airframe_reference.html#vtol_standard_vtol_generic_standard_vtol), Tailsitter, Plane, Rover, Submarine

##### [FlightGear](https://docs.px4.io/main/en/sim_flightgear/)
A simulator that provides physically and visually realistic simulations. In particular it can simulate many weather conditions, including thunderstorms, snow, rain and hail, and can also simulate thermals and different types of atmospheric flows. [Multi-vehicle simulation](https://docs.px4.io/main/en/sim_flightgear/multi_vehicle.html) is also supported.
**Supported Vehicles:** Plane, Autogyro, Rover

##### [JMAVSim](https://docs.px4.io/main/en/sim_jmavsim/)
A simple multirotor/quad simulator. This was previously part of the PX4 development toolchain but was removed in favour of [Gazebo](https://docs.px4.io/main/en/sim_gazebo_gz/).
**Supported Vehicles:** Quad

##### [JSBSim](https://docs.px4.io/main/en/sim_jsbsim/)  (This is the one we are using as a plugin on GODOT engine)
A simulator that provides advanced flight dynamics models. This can be used to model realistic flight dynamics based on wind tunnel data.
**Supported Vehicles:** Plane, Quad, Hex

##### [AirSim](https://docs.px4.io/main/en/sim_airsim/)
A cross platform simulator that provides physically and visually realistic simulations. This simulator is resource intensive, and requires a very significantly more powerful computer than the other simulators described here.
**Supported Vehicles:** Iris (MultiRotor model and a configuration for PX4 QuadRotor in the X configuration).

[Simulation-In-Hardware](https://docs.px4.io/main/en/sim_sih/) (SIH)
An alternative to HITL that offers a hard real-time simulation directly on the hardware autopilot. This simulator is implemented in C++ as a PX4 module directly in the Firmware [code](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/simulator_sih).
**Supported Vehicles:** Plane, Quad, Tailsitter


### What are the differences between these softwares compared to each other?

|                    | Gazebo                                                                          | FlightGear        | JMAVSim        | JSBSim          | AirSim          | SIH                |
| ------------------ | ------------------------------------------------------------------------------- | ----------------- | -------------- | --------------- | --------------- | ------------------ |
| Focus              | Multi-Vehicle, [ROS](https://docs.px4.io/main/en/simulation/ros_interface.html) | Realism & Weather | Basic Quad Sim | Flight Dynamics | Visual Fidelity | Real-time Hardware |
| Visualization      | 3D Functional                                                                   | Realistic         | Basic Graphics | None            | High Fidelity   | Limited            |
| Vehicles Supported | Many                                                                            | Planes, Rovers    | Quads Only     | Planes, Quads   | Multirotors     | Planes, Quads      |
| <br>Performance    | Medium-High                                                                     | Medium            | Low            | High            | High            | Low-Medium         |
### What are the pros and cons of each one?
##### Gazebo
Pros:
- Advanced 3D simulation for object-avoidance and computer vision.
- Supports multi-vehicle simulations.
- Integrates well with ROS for vehicle automation.
- Versatile, supporting multiple vehicle types.
- Great sensor simulation. Simulates a wide variety of sensors.
- Multi-Physics Support. Allows switching between different physics engines to suit specific use cases like fast simulations or precise dynamics.
Cons:
- Requires Ubuntu Linux 22.04 or higher.
- Can quickly be very demanding on system resources.
- Kinda hard to learn.
- Some versions of Gazebo are tied to specific operating system versions. Works best with Ubuntu.
- **Gazebo prioritizes control and system testing over highly accurate aerodynamic simulations. It is less focused on Realistic Aerodynamics.** (Which is the main reason we shouldn't be using Gazebo)

##### Gazebo Classic
Pros:
- Similar capabilities to Gazebo.
- Compatible with many vehicle types.
Cons:
- Outdated compared to the newer Gazebo version. Why use the former one?

##### FlightGear
Pros:
- Highly realistic physical and visual simulations.
- Simulates diverse weather conditions.
- Supports multi-vehicle simulation.
Cons:
- The high realism in physics and visual rendering can be demanding on hardware.
- Flightgear's detailed setup, including environmental configurations, flight dynamics and controls can be hard for beginners.
- While FlightGear supports multi-vehicle simulations it is not very optimized for multi-robot or multi-drone scenarios.
- FlightGear does not support rich 3D interaction (like collision testing, object manipulation) as well as other simulators like Gazebo. If we need interaction between objects or other UAVs we cannot trust FlightGear.
- As it is good for aviation training, research and testing, it lacks performance for tasks like object detection or control system testing.

##### jMAVSim
Pros:
- Simple and lightweight.
Cons:
- jMAVSim allows you to fly *copter* type vehicles. We can't use it.

##### JSBSim
Pros:
- Advanced flight dynamics based on real-world wind tunnel data.
- It is lightweight. Unlike 3D simulators, JSBSim is text-based and does not include a GUI (also a con).
- Supports various vehicles like planes and multirotors.
- Runs on multiple operating systems. (Windows, Apple Macintosh, Linux...)
- Fully configurable aerodynamics and propulsion system that can model complex flight dynamics of an aircraft.
- Works as a standalone simulator or integrates as a plugin with other engines like Godot, Gazebo or FlightGear.
Cons:
- Focused on flight dynamics rather than 3D visualization.  JSBSim doesn't include a 3D environment or GUI. Users must integrate it with tools like Godot for visual simulation.
- Primarily designed for aircraft and doesn't natively support other types of vehicles like rovers or boats. (It is not a con for us I think.)
- Aircraft and simulation setup requires detailed XML files and a deep understanding of flight dynamics and system modelling.
- No built-in multi-vehicle support. 
- It's great for accurate aerodynamics and flight physics but lacks support for detailed system simulations like object avoidance or sensor modelling. May require pairing with other simulators for comprehensive robotics testing.
- Fewer tutorials as it has a smaller community than broader simulators like Gazebo or FlightGear.

##### AirSim
Pros:
- AirSim uses Unreal Engine. It has very realistic environments, including landscapes, cities and indoor spaces.
- AirSim has realistic sensor simulations. Simulates a wide variety of sensors, such as cameras (RGB, depth, infrared), LiDAR, GPS, IMU and barometers.
Cons:
- As it is a great simulation, it is a simulator for drones. We can't use it.

##### Simulation-In-Hardware (SIH)
Pros:
- Hard real-time simulation directly on autopilot hardware.
- Lightweight as it runs within PX4 firmware.
- Ideal for Initial Testing.
Cons:
- Limited to specific PX4-supported configurations.
- No 3D visualization.
- SIH does not simulate complex environments or weather conditions like wind, rain or uneven terrains. 
- Limited scope for non-autopilot features. SIH focuses on validating control and dynamics but lacks capabilities for testing higher-level behaviours like obstacle avoidance or mapping.

### What requirements do we have about the simulation to accurately model the mission scenarios from the competition?

#### 1) Realistic Physics and Dynamics:
- Aerodynamic Modelling: Implement detailed aerodynamic models to simulate flight characteristics, including lift, drag and thrust.
- Collision Detection: Simulation must detect and respond to collisions between UAVs and with environmental obstacles.
#### 2) Sensor Simulation:
- Visual System: Simulate cameras and other visual sensors to enable autonomous target detection and tracking. This is very *important* for us as we have Kamikaze and Fighting UAV missions.
- Measurement: Provide accurate data on acceleration and orientation to replicate real-world sensor feedback.

#### 3) Environmental Factors
- Weather Conditions: Simulation must include variables such as wind to affect UAV performance.
- Terrain and Obstacles: Model the competition environment, including ground features and potential obstacles. 

#### 4) Autonomous Behaviour and Control
- Flight Control Systems: Develop algorithms for stable flight, manoeuvring and mission-specific tasks like target locking and kamikaze dives.
- Decision-Making Algorithms: Implement AI for real-time decision-making during engagements.

#### 5) Multi-UAV Interaction
- Swarm Dynamics: Simulate interaction between multiple UAVs, including coordination and collision avoidance.
- Communication Protocols: Model data exchange between UAVs for coordinated missions.

#### 6) Real-time Performance
- Low Latency: Simulation must operate in real-time to accurately reflect UAV responses.
- Scalability: Maintain performance with multiple UAVs and complex environments.

#### 7) Integration with Control Systems
- Hardware-in-the-Loop (HIL): Allow integration with actual UAV hardware for testing.
- Software-in-the-Loop (SIL): Enable testing of control algorithms within the simulation.

#### 8) Data Logging and Analysis
- Telemetry Recording: Capture flight data for post-mission analysis.
- Performance Metrics: Evaluate mission success criteria, such as target engagement accuracy and mission completion time.

### What additional functionality can be added to existing simulations so we can better model mission scenarios?

#### 1) Enhanced Environmental Realism
- Dynamic Weather Conditions: Introduce real-time changes in weather such as rain, snow, wind gusts or even turbulence that affect UAV performance.
- Complex Terrain Modelling: Add detailed terrains with varied topographies, based on TEKNOFEST competition area.
- Obstacle Population: Maybe simulate dynamic obstacles like moving vehicles or pedestrians for urban scenarios.

#### 2) Advanced Physics and Aerodynamics
- Damage Modelling: Simulate UAV damage during collisions, impacting flight dynamics. We should see what happens if we mistakenly hit something like watering canal. (!)
- Fuel/Energy Consumption: Add real-time tracking of battery usage or fuel consumption.

#### 3) Target Simulation
- Intelligent Targets: Simulate targets with AI-driven behaviour, such as evasive manoeuvres.
- Target Types: Include various target types like stationary, mobile, airborne to test adaptability. As we have 2 missions, one being on airborne and one being on ground we must be able to target both of them.
- Radar and Signal Simulation: Add systems to simulate the detection and tracking of targets via radar or other signals.

#### 4) Multi-Agent Interaction
- Cooperative UAV Missions: Simulate swarm behaviours where UAVs work together to achieve a mission or make a show to other competitors.
- Enemy AI: Add intelligent opponent UAVs that act as competitors or aggressors in Fighting UAVs mission.

#### 5) Advanced Sensor Modelling
- Enhanced Vision Systems: Simulate high-resolution cameras, thermal imaging or night vision systems.
- Sensor Breakdown: Model sensor inaccuracies caused by environmental conditions or interference.

#### 6) Mission-Specific Modules
- Kamikaze UAV Dynamics: Add functionality to model rapid dives and target locking.
- Combat Manoeuvres: Include functionality for evasive and aggressive flight patterns.

#### 7) Real-Time Monitoring and Feedback
- Live Telemetry: Enhance the simulation with a dashboard displaying real-time data such as speed, altitude and mission status.
- Mission Replay: Add functionality to replay missions for performance review and debugging.
- Mission Speed: Add functionality to slow down or speed up the simulation.

#### 8) Extended Interoperability
- Hardware Integration: Support HIL testing with autopilot systems and sensors.
- Software API Extensions: Allow integration with AI frameworks, ROS or custom control software.
- Communication Protocols: Add simulation for inter-UAV communication using standardized protocols.

#### 9) Training and Testing Tools
- Scenario Customization: Allow users to design custom mission scenarios with adjustable parameters.
- AI Testing Framework: Integrate reinforcement learning environments to train UAVs in the simulation.
- Failure Modes: Add simulated failures (e.g. engine loss, sensor malfunctions) to test system robustness.

#### 10) Visual Enhancements
Enhanced Graphics: Improve textures, lightning and effects for better graphics.
Multi-Camera Views: Enable views from multiple UAVs and ground stations simultaneously.

### How can we implement a simulation integrated with PX4 ourselves?
Implementing a simulation integrated with PX4 involves creating a virtual environment where the PX4 autopilot firmware interacts with simulated vehicles and environments as if they were real.
#### 1) Understand PX4 and Its Ecosystem
- PX4 is an open-source flight control software supporting various vehicles (drones, planes etc.).
- Supported Simulators: PX4 integrates seamlessly with simulators like Gazebo, JSBSim and Flightgear. You can find the list on other sections of this report.
- Middleware: PX4 uses MAVLink, a lightweight communication protocol, to exchange data between the autopilot and the simulation environment.

#### 2) Choose a Simulation Platform
We made all the comparison between the simulation platforms. 
- Gazebo: Best for 3d environments, sensor simulation and robotics integration.
- Best for precise flight dynamics.
- Custom Simulations: If specific requirements aren't met by existing tools, we can create a custom simulation using tools like Unreal Engine, Unity, Godot or our own framework.

#### 3) Set Up the Development Environment
- Install PX4 Firmware
- Install Required Tools: Install JSBSim or other chosen simulation platforms. And also install MAVLink tools and PX4 development dependecies.

#### 4) Integrate the Simulator with PX4
- We can find the commands on chosen simulation platform.
- Custom Simulator Integration:
	- Write a MAVLink client in your simulation to send/receive data from PX4.
	- Define messages like position, velocity and sensor data using the MAVLink protocol.

#### 5) Define Your Simulation Environment
- Vehicle Models: Create or customize vehicle models in the simulator. (e.g. fixed-wing aircraft)
- Environment: Add terrain, obstacles, and environmental factors (e.g. wind) to your simulation.

#### 6) Implement Sensors and Actuators
- Sensors:
	- Simulate sensors like GPS, LiDAR, IMU and cameras.
	- If you are using an simulation platform like Gazebo, use built-in plugins or custom ones to emulate sensor outputs.
- Actuators:
	- Simulate motor dynamics, servo controls and other actuators responding to PX4 commands.

#### 7) Test and Debug
- Run Simulation:
	- Use the PX4 SITL (Software-In-The-Loop) configuration to test without hardware.
	- Launch PX4 and the simulator then monitor behaviour via QGroundControl or a custom GUI.
- Debugging Tools:
	- Use logs, MAVLink inspector and PX4's built-in analysis tools to debug and refine.

#### 8) Customize for Our Needs
- Mission Scenarios:
	- Program autonomous behaviours in PX4 (e.g. mission waypoints, collision avoidance).
	- Use MAVSDK or MAVROS to write high-level mission control scripts.
- Integration with AI:
	  Train AI models in the simulation for tasks like object detection or target racking.
	  Integrate AI outputs with PX4 using MAVLink.

#### 9) Expand with Hardware-In-The-Loop (HIL)
- Replace SITL with HIL for testing on real autopilot hardware.
- Connect your PX4 autopilot hardware to the simulator, ensuring real sensors and actuators interact correctly.

#### 10) Document and Iterate
- Keep logs of testing scenarios, outcomes and modifications for continuous improvement.
- Iterate based on performance metrics, debugging results and TEKNOFEST requirements.

### How can we systematically measure the success of the missions we tested in simulation and analyse them for further improvement?

#### 1) Define Success Metrics
##### Mission-Specific Metrics
- Target Acquisition:
	- Time taken to detect and lock onto a target.
	- Accuracy of target identification.
- Engagement Effectiveness:
	- Success rate on Kamikaze and Fighting UAVs target lock-in.
	- Avoidance of getting locked from enemy entities.
- Mission Completion Time:
	- Total time taken to complete the mission objectives.
##### Flight Performance Metrics
- Stability:
	- Deviation from desired flight paths or waypoints.
	- Oscillations or overshoots in control responses.
- Energy Efficiency:
	- Battery consumption per mission.
- Environmental Adaptability:
	- Ability to perform under varying conditions (e.g. wind, obstacles).
##### Safety and Reliability Metrics
- Collision Rate:
	- Number of unintended collisions with obstacles, terrain or other UAVs.
- Recovery Capability:
	- Ability to recover from simulated faults or malfunctions.

#### 2) Record Simulation Data
##### Telemetry Logging
- Sensors: Log data from simulated sensors (e.g. GPS, IMU, LiDar) to evaluate sensor accuracy.
- Commands: Record control commands sent by the autopilot or mission planner.
- State Variables: Capture variables such as speed, altitude, orientation and position.
##### Mission Events
- Timestamps significant events like:
	- Target detection and engagement.
	- Waypoint completition.
	- Mission success or failure triggers.
##### Environment and Conditions
- Record simulation parameters such as wind speed, visibility and obstacle positions.
##### Tools for Logging
- PX4 Logs: Use PX4's built-in logging capabilities (ulog files) to capture detailed flight data.
- External Tools:
	- QGroundControl for visualizing and analysing telemetry.
	- MAVLink-based tools for custom data logging.

#### 3) Analyse Data
##### Performance Visualization
- Use plotting tools like Python (Matplotlib, Seaborn), MATLAB or Excel to:
	- Visualize flight paths and deviations.
	- Analyse speed, energy consumption and sensor accuracy over time.
##### Event Correlation
- Identify correlations between environmental factors and mission outcomes (e.g. increased wind correlates with reduced stability).
#### Error and Failure Analysis
- Investigate causes of mission failures:
	- Examine logs for abnormal sensor readings or control inputs.
	- Recreate failure scenarios to isolate root causes.
##### Statistical Analysis
- Compute averages, variances, and success rates over multiple simulation runs.
- Use techniques like [Monte Carlo](https://tr.wikipedia.org/wiki/Monte_Carlo_yöntemi) simulations to test robustness under varied conditions.

#### 4) Implement Feedback Mechanisms
##### Scenario Refinement
- Modify simulation environments to include more challenging or varied scenarios based on observed weaknesses.
- Add dynamic targets or harsher weather conditions to test robustness.
##### Algorithm Improvement
- Update flight control or decision-making algorithms to address observed shortcomings (e.g. better path planning or collision avoidance).
- Fine-tune parameters such as PID gains, target detection thresholds or energy management strategies.
##### System Upgrades
- Improve simulated UAV hardware, such as adding better sensors or more efficient propulsion systems, based on performance gaps.

#### 5) Validate Improvements
- Re-run simulations with updated algorithms, configurations or scenarios.
- Compare new results against baseline metrics to quantify improvements.

#### 6) Document and Review
- Create comprehensive reports summarizing:
	- Mission objectives, results and metrics.
	- Key successes and areas for improvement.
	- Action items for further refinement.
##### Team Collaboration
- Conduct post-simulation reviews with our team to:
	- Discuss findings and potential improvements.
	- Decide on priorities for the next development cycle.

#### 7) Automate the Analysis Process
- Develop scripts or tools to automatically:
	- Parse and analyse logs.
	- Generate performance reports.
	- Flag potential issues or anomalies.

By systematically measuring success through these steps, we can continuously refine our UAVs and mission strategies, ensuring readiness for real-world deployment or competition scenarios like TEKNOFEST.