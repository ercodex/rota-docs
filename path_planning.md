# What's difference between Path Planning and Motion Planning terms?

Actually, this question depends on situation. Consider that a man walking on a rope in a cirque. There is only one path: the rope. The motion planning on the other hand is not that easy.  On the other hand, in a maze the path planning is hard and motion planning is easy. Of course both planning tasks can be easy or hard at the same time or anything in between.

Motion is related to time (change of position over time), but the path itself is not. Even if the motion goes along the path exactly it could have various trajectories for velocity and/or acceleration. Your robot could stop for example to let another robot cross its path, which is a change in motion but not the path itself.

For anything that involves moving a physical robot, we should use motion planning. How do we get this thing from here to there? could be a question we are answering.

If the robot itself is abstracted away and we are only planning a path of a point through a maze we should use path planning. What's the shortest path from point A to B? could be an example question.

But again, both planning tasks are connected. If you ask What's the shortest possible path? then certainly the geometry and motion capabilities of the robot determine what's possible. We do both motion and path planning at the same time. Using either term should be fine here.

**In short, path planning is the process you use to construct a path from a starting point to an end point given a full, partial or dynamic map. Motion planning is the process by which you define the set of actions you need to execute to follow the path you planned. 

Because of an UAV including many parameters to motion such as speed and acceleration restrictions, minimum turning radius and maximum maneuvering capacity etc, ROTA's motion planning will include path planning and path tracker terms.

Since our UAV will use path finding algorithms in both tracking, kamikaze and escape missions from prohibited areas, we need to use methods that will create a route with maximum performance and minimum time. At the same time, the route created must be arranged in a format that the UAV's flight control system will understand and operate, and it must be created in accordance with physical restrictions.

Last year, we used RRT* algortihm and Dubins Path to solve this problem:

RRT-RRT*: Rapidly-exploring Random Tree,  is an algorithm designed to efficiently search nonconvex, high-dimensional spaces by randomly building a space-filling tree. RRT* is an improved version of RRT and provides shorter and more efficient routes by optimizing the routes it finds. 

Dubins Path: Dubins Paths is a method used to plan the shortest path for vehicles with certain kinematic constraints. It is especially suitable for vehicles moving with a constant turning radius and constant speed. Dubins Paths consists of three main movement segments (straight Deceleration, left turn and right turn), and the combination of these movements creates the shortest path between the vehicle's starting and finishing positions.

Dubins Path's Segments: LSL, RSR, LSR, RSL, LRL, RLR. 

We were converting the optimal route created by the RRT* algorithm to dubin paths units by invalidating nodes outside the allowed flight areas. Our most important goal here was to avoid no-fly areas. And this technique was our main path planning method.

# What is UAV path planning?
Among the many open issues to be addressed in the development of UAVs is that of path planning [2]. Path planning considers the problem of designing the path a vehicle is supposed to follow. Along the designed path the objectives are to maximize the collected information (CI) from desired regions (DR), while avoiding flying over forbidden regions (FR) and reaching the destination [3]. UAV path planning involves the pre-flight formulation of an optimal reference trajectory that satisfies constraints based on environmental information and mission requirements [1].
# What is a global planner in robotics?
Global planning refers to the comprehensive strategy and decision-making process that autonomous robots use to determine their paths and actions while taking into account the entire environment they operate in [6]. 

1. Global planning is crucial for autonomous robots operating in complex environments where obstacles can vary dynamically.
2. Algorithms like A* or Dijkstra's are often used in global planning to find the most efficient paths by analyzing grid-based maps.
3. Effective global planning must balance computational efficiency with the need for accurate obstacle detection and avoidance.
4. It often incorporates techniques such as heuristics or optimization methods to enhance decision-making in real-time scenarios.
5. Global planning is not only about avoiding static obstacles but also considers moving obstacles, requiring predictive modeling.

- **How does global planning differ from local planning in the context of autonomous navigation?**

  Global planning focuses on determining the overall route that an autonomous robot should take from its starting point to its destination by analyzing the entire environment. In contrast, local planning deals with immediate movements and obstacle avoidance in close proximity to the robot. While global planning sets the long-term strategy, local planning ensures that the robot can navigate around obstacles in real-time, adjusting its movements based on immediate environmental changes.
  

Terms related to global planning: path planning, dynamic environments, sensor fusion.

**Path Planning:** 
Path planning is the process of determining a route or trajectory for a robot to follow in order to reach a desired destination while avoiding obstacles and optimizing specific criteria. This concept is crucial for robots to navigate their environments effectively, as it involves considerations of the robot's components, dynamics, and the terrain or surroundings they operate within.

-Path planning can be executed using various algorithms, such as A*, Dijkstra's algorithm, or Rapidly-exploring Random Trees (RRT).
-In collaborative robotics, path planning must account for the movements of multiple robots to avoid collisions and improve efficiency.
-The effectiveness of path planning relies heavily on accurate mapping and localization of the robot within its environment.

**Dynamic Emvironments:** 
Dynamic environments refer to settings where conditions change rapidly and unpredictably, affecting how autonomous robots operate. In these environments, factors such as moving obstacles, varying terrain, and fluctuating weather can impact sensor readings and navigation strategies. Adapting to these dynamic conditions is crucial for effective mapping and path planning.

-Robots must employ advanced algorithms to adapt their strategies in real-time when faced with changes in dynamic environments.
-These environments often require robots to prioritize obstacle detection and avoidance to ensure safe navigation.
-Dynamic environments are common in applications like search and rescue missions or autonomous vehicles, where conditions can shift quickly.
-Robust communication between sensors and processing units is essential for robots to effectively respond to changes in their surroundings.

**Sensor Fusion:** 
Sensor fusion is the process of combining data from multiple sensors to produce more accurate, reliable, and comprehensive information about an environment or system. By integrating different sensor inputs, such as visual, auditory, and positional data, it enhances the overall understanding and perception of a robotic system, allowing for improved decision-making and navigation.

-Sensor fusion improves accuracy by reducing uncertainty and noise inherent in individual sensor readings, resulting in better environmental perception.
-Different types of sensors can complement each other, such as using cameras for visual data and Lidar for distance measurements to enhance depth perception.
-The efficiency of sensor fusion algorithms can significantly impact the responsiveness and reliability of autonomous systems in dynamic environments.
-Applications of sensor fusion are found in various fields, including autonomous vehicles, robotics, and augmented reality, where real-time data integration is crucial.
-By effectively combining data from multiple sources, sensor fusion helps robots make better-informed decisions regarding navigation and obstacle avoidance.
# What are the algorithms currently used by the industry?

Path planning algorithms are crucial in robotics, enabling robots to navigate complex environments efficiently. **A*, RRT**, and **PRM** are three key algorithms used for this purpose, each with unique strengths and applications [5].

These algorithms differ in their approach to finding paths. A* uses heuristics for informed search, RRT builds trees to explore space, and PRM creates roadmaps for repeated queries. Understanding their implementations and trade-offs is essential for effective robot navigation [5].

**Fundamentals of A* search algorithm**

- [**A* algorithm**](https://library.fiveable.me/key-terms/robotics/a-algorithm) combines [**Dijkstra's algorithm**](https://library.fiveable.me/key-terms/robotics/dijkstras-algorithm) and greedy best-first search uses [**heuristic**](https://library.fiveable.me/key-terms/robotics/heuristic) function to estimate cost from current node to goal
- Key components include open list for nodes to evaluate closed list for evaluated nodes and f(n)=g(n)+h(n)  where g(n) is actual cost from start to current node and h(n) is estimated cost to goal
- Algorithm steps involve initializing open list expanding node with lowest f(n)  generating successors calculating f(n) values adding to open list if not in closed list moving expanded node to closed list repeating until goal reached or open list empty
- Heuristic function must be admissible never overestimating cost to goal and consistent satisfying triangle inequality
- Examples: Manhattan distance for grid-based pathfinding Euclidean distance for continuous spaces  [5]

**Implementation of RRT algorithm**

- RRT incrementally builds tree to explore configuration space biases growth towards unexplored regions
- Key components include root node random sampling nearest neighbor search and local planner for connecting nodes
- Algorithm steps involve initializing tree generating random sample finding nearest node extending tree checking for collision adding new node repeating until goal reached or maximum iterations
- Variants include [**RRT***](https://library.fiveable.me/key-terms/robotics/rrt) for asymptotic optimality and [**Bidirectional RRT**](https://library.fiveable.me/key-terms/robotics/bidirectional-rrt) growing trees from both start and goal
- Applications in high-dimensional spaces robot arm motion planning (6+ DOF manipulators) nonholonomic vehicle path planning (car-like robots) [5]

**Application of PRM algorithm**

- PRM uses two-phase approach roadmap construction and query phase builds graph representation of free space
- Roadmap construction involves random sampling collision checking connection of nearby configurations
- Query phase connects start and goal to roadmap searches for path using graph search algorithm (A*)
- Key components include sampling strategy (uniform obstacle-based) collision detection module local planner nearest neighbor search
- Variants include [**Lazy PRM**](https://library.fiveable.me/key-terms/robotics/lazy-prm) deferring collision checking to query phase [**Adaptive PRM**](https://library.fiveable.me/key-terms/robotics/adaptive-prm) adjusting sampling density based on local difficulty
- Examples: multi-robot coordination in factory settings path planning for unmanned aerial vehicles (UAVs) in complex environments [5]

**Performance analysis of path planning algorithms**

- A* time complexity O(bd) space complexity O(bd) performance affected by heuristic function quality graph connectivity
- RRT time complexity O(nlogn) space complexity O(n) performance influenced by sampling strategy step size environment complexity
- PRM roadmap construction complexity O(n2) query phase depends on graph search algorithm performance affected by sampling density connection radius environment complexity
- Algorithms compared in low vs high-dimensional spaces (2D grid world vs 6-DOF robot arm) static vs dynamic environments (fixed obstacles vs moving obstacles) single vs multiple query problems (one-time path planning vs repeated queries)
- Trade-offs include completeness (A* complete RRT and PRM probabilistically complete) optimality (A* optimal RRT and PRM suboptimal) scalability to high-dimensional spaces (RRT and PRM generally better) [5]

# Which algorithm is more suitable for us?

The **RRT and RRT* algorithms** may be more suitable.

The reason for it:

RRT or RRT*; Can quickly create valid paths for UAVs operating with dynamic obstacles or in unknown areas. RRT* may be preferred because it finds more optimized ways.

**Unknown or Dynamic Environments**

•        UAVs usually operate in unknown or constantly changing environments (for example, drones roaming around cities or UAVs conducting reconnaissance in natural disaster zones). In these environments:

Sudden appearance of obstacles: RRT can quickly create a new path by expanding its existing tree in case of new obstacles appearing.

•        Rapid exploration in unknown areas: RRT quickly discovers the environment with random sampling and creates suitable paths.

**Real-Time Planning**

•        RRT is computationally light thanks to random sampling and can find a valid way in a short time. This is very important when UAVs need to do real-time planning. For example:

In an environment where obstacles are displaced, RRT can quickly create a new path by updating the existing path tree.

•        RRT*, on the other hand, optimizes the path it finds in this process, resulting in less energy consumption or shorter travel time.

**Three-Dimensional Motion Planning**

•        UAVs, unlike terrestrial vehicles, move in a three-dimensional space (x, y, z). RRT and RRT* can also be easily applied in three-dimensional spaces:

The tree structure can be expanded through three-dimensional coordinates.

•        Special situations, such as flight corridors or airspace restrictions, can be added to the algorithm.

**Flexibility**

•        Interaction with Dynamic Obstacles: The RRT algorithm can quickly revise the valid paths in the event of an obstacle appearing.

•        Lack of Information: RRT can work without a complete map. Therefore, it is ideal for UAVs that work with sensors such as GPS, lidar, camera.

-------------------------------------------------------------------------------------------------------------------------

**  The use of the A* algorithm is suitable for Static Maps and Planned Tasks

**  The PRM is suitable for UAVs conducting reconnaissance over a wide area or operating in less dynamic environments. However, when the environment is variable, the ability to plan instantly may be limited.

# How can we use those algorithms with our system?
The RRT* algorithm can be the starting point for creating fast and valid routes; this algorithm can plan flight routes to stay within limits, taking into account both static and dynamic obstacles. For interaction and Deceleration scenarios between UAVs, the A* algorithm can be used to avoid collision with other UAVs while finding the most optimized route to the target. These algorithms can be integrated with real-time data (for example, location information of other UAVs and environmental changes) to create a dynamic flight control system. Thus, UAVs can act in harmony with each other, comply with environmental restrictions and effectively track targets.

# How can we evaluate the performance of those algorithms so we can select the best one?
Several basic criteria can be used to evaluate the performance of these algorithms: calculation time, path length and optimization, ability to adapt to dynamic environment, collision avoidance success and resource utilization . In test scenarios, algorithms should be run in real-time situations (e.g. moving target tracking, boundary violation blocking) and the results should be compared according to these metrics. In addition, the accuracy and flexibility of the algorithms should be observed in situations where dynamic obstacles are intense and require interaction with other UAVs. In this way, the most appropriate algorithm can be selected for the needs of the system.


# REFERENCES

[1]     U. M. Ijaz, I. Naseem, and M. A. Iqbal, “A new method for unmanned aerial vehicle path  planning in complex environment,” _Scientific Reports_, vol. 14, Art. no. 60051, 2024. [Online]. Available: [https://www.nature.com/articles/s41598-024-60051-4](https://www.nature.com/articles/s41598-024-60051-4)

[2]    J. A. Starek and M. P. Dubins, “Path planning for UAVs,” in _Proceedings of the 2000 American Control Conference_, Chicago, IL, USA, Jun. 2000, pp. 364–368.

[3]     H. Ergezer, “Path planning for UAVs for maximum information collection,” _ResearchGate_, 2014. [Online]. Available: [https://www.researchgate.net/profile/Halit-Ergezer-2/publication/258792372_Path_Planning_for_UAVs_for_Maximum_Information_Collection/links/5437ea1c0cf2d5fa292b7962/Path-Planning-for-UAVs-for-Maximum-Information-Collection.pdf](https://www.researchgate.net/profile/Halit-Ergezer-2/publication/258792372_Path_Planning_for_UAVs_for_Maximum_Information_Collection/links/5437ea1c0cf2d5fa292b7962/Path-Planning-for-UAVs-for-Maximum-Information-Collection.pdf). [Accessed: Nov. 23, 2024].

[4]    Maizi, “Global path planning,” _Medium_, Oct. 5, 2020. [Online]. Available: [https://medium.com/@maizi5469/global-path-planning-f277de41249d](https://medium.com/@maizi5469/global-path-planning-f277de41249d). [Accessed: Nov. 23, 2024].

[5]    “Path planning algorithms: A*, RRT, PRM,” _Fiveable Library_. [Online]. Available: [https://library.fiveable.me/robotics/unit-7/path-planning-algorithms-a-rrt-prm/study-guide/2Y6L25zBjDRe24Df](https://library.fiveable.me/robotics/unit-7/path-planning-algorithms-a-rrt-prm/study-guide/2Y6L25zBjDRe24Df). [Accessed: Nov. 23, 2024].

[6]    “Global planning,” _Fiveable Library_. [Online]. Available: [https://library.fiveable.me/key-terms/introduction-autonomous-robots/global-planning](https://library.fiveable.me/key-terms/introduction-autonomous-robots/global-planning). [Accessed: Nov. 23, 2024].
