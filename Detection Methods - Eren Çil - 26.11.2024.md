# Goal:

**Detect and track enemy UAV seen on imaging sensors.**

---

# **Topics of Research:**

- _**How can we use added information from other sensors to better accomplish this task?**_
    
    - IMU provides bunch of useful data to ensure the reliable functioning of the process. It is an essential component of the plane. However, the **data it provides is about** **our UAV**, **not other ones**. Other than **verification purposes**, I don't think IMUs are particularly useful for locking onto enemy UAVs.
    - They can be used to **prevent crashes** though. We can implement some **real-time predictions about both plane's further location**. If algorithm decides that they will crash according to data it reads from IMU, **route changes** would be applied. PX4 already has most of these. We talked about **customizing PX4** to our needs. I think it will be the best (If we will be able to do it 🙂).
    - Now I want to talk about other cameras. Since we can't use some specific sensors (e.g., LIDAR), it is essential to obtain **high-quality and processable data from rear and side cameras** as much as possible. These can be used to **detect enemy UAVs** around us, and this data can then help in making a reasonable decision about which UAV to follow to shoot. Other than that, we can decide whether there is an **enemy** **UAV following us or not**. If so, we would try some **escaping manevours**. I think **preventing other teams from earning points** is as important as earning a point ourselves. We can also check whether we are able to escape follower UAV. Another thing I want to mention here is, we should **research and learn most commonly used algorithms** between participants, even though we don't use it. Because, if we understand how model is going to behave, the **chances of tricking follower UAV** goes high. We can implement these specific manevours to our plane. Also there should be a **deciding system** that decides which operation is most urgent to perform on air, fight or flight.
    - We can use military knowledge to our advantage too. If we can feed our plane with **real dogfight tactics** in simulation, it will be a plus too.
    - I think we can create a **hybrid training environment**. **LIDAR sensors** are illegal in competition, but I think we can **use them to train our model**. We can **associate** both LIDAR and camera data. We feed them both to a **model that finds correlations** between them. Then in competition phase when plane encounters with a familiar situation, as if it has LIDAR sensor, it can use its power to **make better decisions**.
    - While talking about training, what about buying a cheap drone and consider it as an enemy UAV? With this, we would see whether our plane is effective in real-life or not, before going to competition.
    - There is also a **disadvantage, latency**. When we got data from servers, it is already **outdated**. To solve this problem, here is some of the solutions:
        - Adding a **timestamp** to data. This way, we wouldn't be bothered with whether data transfer is fast enough.
        - We can use **guessing algorithms** feeded with both IMU and front camera to reduce latency to minimum via guessing the next position of the plane (Kalman Filters).
        - We can purely predict what is the next position via **physical and mathematical equations**. It would be very fast I think. But would it be successful? Or, we can **empower it with ML-based models like LSTM and RNN**.
        - We can add "**trust weight**" to data. The **older the data, lower the trust**. To empower this, we can use a hybrid system to fuse every data type together with their weight.
        - We can use a ML model to decide how reliable is current data to trust. If we create model that understands the difference and patterns between actual and old data, we can predict better.
    
    ---
    
- _**How can we use our specific condition (being in the air with limited moving objects other than enemy teams etc.) to better accomplish this task?**_
    
    - We have some advantages in our condition, yes. We could transfer **computation power** we got to different tasks (maybe ones we say “nice to have, but not essential”) if we **reduce the costs of algorithms** we use. We may find a **very simple, very effective algorithm** which gets the same job done (maybe better!).
    - I don’t think there is much “distractions” for our object detection algorithm, especially while on air. **Large models wouldn’t be needed** I assume. As I mentioned before, some **filtering** can be applied **while transferring data** to improve efficiency. Because there is no need to have colors, details of enemy UAVs, or landscape details. We can hard-code to our software that if axis values are blablabla, then this pixels you see in specific locations on screen should be landscape, ignore it. Very basic but potentially a good method. I am aware that this also raises some concerns like “what if enemy UAV has landscape in its background?”. It can be talked later in more detail. Just thinking out loud.
    - We can use the **speed of enemy UAVs** to improve our detection method. By filtering for objects moving above certain speeds, we can ignore stationary or slow-moving objects. However, I'm uncertain about the practical benefits of this approach.
    - I didn’t see any limitations about that but, we can use **aircraft camouflage** on our plane to prevent some enemy UAVs to detect us.
    - Here is some challenges we face with enemy UAV detection and tracking:
        
        - Environmental Factors:
        - Variable illumination conditions affecting image quality
        - Rapid changes in target scale and perspective
        - Frequent occlusion by clouds, other UAVs, or obstacles
        - Motion blur due to high-speed movement
        - Hardware Limitations:
        - Camera sensor limitations in extreme lighting
        - Processing delays affecting real-time tracking
        - Limited computational resources for complex algorithms
        - Potential signal interference or communication delays
        - To address these challenges, we should consider:
        
        1. Developing robust models trained on diverse conditions
        2. Upgrading hardware components (especially cameras)
        3. Implementing redundant detection systems
        4. Creating fallback protocols for degraded conditions
- _**Which state-of-art techniques are used on similar problems?**_
    
    - I read a survey article to achieve what is the current solutions. I reached **A LOT** of methods. I’ll mention their names.
        
    - In the next version of this paper, I'll **analyze each of these methods** in detail. I'll explain their use cases with **visualizations** and **real-life applications**. Then I'll group and evaluate them analytically to learn which suits us best.
        
    - There are 4 types of tracking methods.
        
        ---
        
        - **Feature Based (Feature Extraction and Similarity Metrics):**
            - Color Histogram
            - Gabor Filters
            - Local Binary Patterns
            - Optical Flow
            - Optical Flow + HOG
        
        ---
        
        - **Segmentation Based (Foreground Discrimination):**
            - Bottom-Up Based
            - Joint Based
        
        ---
        
        - **Estimation Based (Framing Tracking using Bayesian Filters):**
            - Kalman Filters (linear)
            - Extended Kalman Filters (non-linear)
            - Particle Filter
            - Monte Carlo Statistical Calculation Method
        
        ---
        
        - **Learning Based:**
            - **Discriminative Methods (Classifiers):**
                - Shallow Learning (SVM Decision Trees, Random Forests)
            - **Deep Learning:**
                - **Feature-Extraction-Based Methods:**
                    - Faster RCNN
                    - GOTURN
                    - KCF2
                    - SSD, YOLO, LSTM
                - **End-to-End Methods:**
                    - Siamese Trackers (GOTURN)
                    - Patch Learning (MDNET)
                    - Graph-Based Trackers (TCNN)
            - **Generative Methods:**
                - Learning object appearance while ignoring background distractions
            - **Reinforcement Learning:**
                - Policy and Matching Networks
- _**Is it possible to implement said techniques? If so, how can we do it?**_
    
    - **Machine learning** can still be used. In one method, they labeled **face area** as 1, and **background area** as 0. After labeling, they feed the **SVM model** with this data. Then they got great results. It was in 2007 though.
    - **Shallow Learning:**
        - Our problem's some parts can be turned into a **classification problem**. For example, discriminating **background and target**. And can be used with **efficient classification models**.
        - **Shallow learning** is a practical choice for targeting enemy drones due to its **efficiency** in **real-time applications** and **low computational requirements**. It relies on **predefined features** such as **HOG** or **LBP**, which are effective for distinguishing objects in controlled environments.
        - Unlike **deep learning**, it does not require **extensive labeled datasets**, making it suitable for scenarios with limited data availability. Additionally, **shallow models** can be quickly trained or adapted to focus on specific target characteristics, enabling faster deployment. It is a balance of **speed, simplicity, and adaptability**.
    - We can use **DL** **just** for **feature extraction**. It is what they good at.
    - We should decide which parts use **online** and which parts use **offline training**.
    - We can separate problems into two parts and may use different **models** or **techniques** to achieve better results. But we need to be careful about whether **output data** is compatible with each other. For example, one model just handles **feature extraction** and other generates **tracking results**.
- _**Can commonly used algorithms like yolo detectors solve our problem? If so, what do we do and consider?**_
    
    - We need to consider some metrics to decide which methods to use in the process. I'll create a ranking table with algorithms performance on a scale from 1 to 5 in each metric. These metrics are:
    - **Compatibility:** This is important when integrating data from different sensors. Generally, outputs should be compatible with each other.
    - **Speed:** This is a critical metric. The algorithms must operato with minimal latency. Other metrics are really related with speed too. Any change means, they change too.
    - **Accuracy:** Algorithms must reliably identify and track enemy UAV, even in dynamic or challenging conditions. We may need to use slower models to achieve more accuracy. It's the part of the game.
    - **Efficiency:** Given hardware limitations, the algorithms should be optimized to minimize computational load.
    - **Error Tolerance:** It is the ability of the algorithm to handle noisy data, sensor errors, data loss etc. Higher error tolerance means more stability.
    - **Generalibility / Adaptability:** Enviromental changes will effect us. Such as varying light, obstacles, changes in target speed etc. DL models typically require more training data but can adapt to a wider variety of enviromental conditions. Shallow learning algorithms, however, might be easier to adapt to more specific conditions.
    - **Scalability:** It is how well the algorithm can scale to handle more targets or more complex environments. For example, as the number of targets increases, how well does the system still perform? YOLO or Faster RCNN can typically handle more complex environments and larger datasets with multiple targets. Whereas simpler algorithms might be limited to tracking just one or a few objects.
    - These metrics will help us when selecting the right algorithm for our task. Each metric has different importance depending on specific needs. By evaluating these metrics, we can make a more informed decision about which algorithm to use, in balance.