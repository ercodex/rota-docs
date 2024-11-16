# What is the GCS ?

**GCS (Ground Control Station)** is basically allows user and **UAV** (Unmanned Aerial Vehicle) interact each other. The basic goal of the software is to provide control of the vehicle, either autonomously, or via pilot input through radio control transmitter or ground control station, or via companion computer on board the vehicle, any of which are optional, including only loading a fully autonomous mission on the vehicle for execution.
To start developing GCS, these topics should be studied and answered carefully.

● How is a GCS used during the flight in context of the competition? 
● What functionality does a GCS have in context of the competition? 
● How do other teams implement and use their GCS? 
● How can we implement needed functionality?
● What technologies do I need to implement it? 
● What technologies do I need to learn? 
● What libraries/frameworks can help me achieve this? 

#### How is a GCS used during the flight in context of the competition and What functionality does a GCS have in context of the competition? 

GCS takes data from UAV (via telemetry device) and referee database (via given ethernet port), at the same time, it uploads data to UAV and referee database (referee database includes GPS data of every competitor UAV with a delay of approximately 1 second).

It also includes autopilot system and RC controller communication system. Most of the time the UAV uses autopilot system to control itself but in cases where intervention is required, a pilot can manage the controls manually with RC controller.

Finally the visual communication is provided with an integrated camera on UAV to Ground Control Station. GCS uses the visual data on integrated AI algorithm which controls the autopilot.

All of those processes are using an user interfaces to ease the interactions.

#### How do other teams implement and use their GCS? 

On my research, I looked [Anatolia Aero Design](https://cdn.t3kys.com/media/upload/userFormUpload/p9ZFgvEsFSS3PSd9qNLrElvuzzx2pp98.pdf)'s and [RECLAB GRIFFIN](https://cdn.t3kys.com/media/upload/userFormUpload/JVdNL010qIf0ypBj3cBfyJClyhVlOBDN.pdf)'s report and those teams are finalists.

Anatolia team using only telemetry system "RDF868" to communicate with UAV. On the other hand, RECLAB team using a long range ~5.8GHz Wi-Fi for SSH(Secure Shell) and if Wi-Fi doesn't work (reasons like over-range), they used the same telemetry system's newer version "RDF900".
Also RECLAB has its own UI design but Anatolia using [ArduPilot](https://ardupilot.org/) software.

Except those, the systems are similar on GCS (they only different about used devices but that devices are similar on algorithm side).
#### How can we implement needed functionality?

In my opinion, we will use similar systems to other competitor teams. We could outshine with using newer versions of equipments. For example, the "RDF868" telemetry device has up to 500 kbit/sec but "RDF868ux" model has up to 750 kbit/sec air data transfer rate. 

Also, I think we should research which system is the most effective way to reach our goals. For instance, RECLAB's own UI looks better than ArduPilot UI but it is not necessary in context of competition. So we need to plan and think very carefully about the steps we will take.
#### What technologies do I need to implement it? 

To implement, I need (probably):
- A telemetry system(and additionally a Wi-Fi solution)
- RC controlling system
- Visual communication system
- User Interface Program
- Possibly machine learning algorithms
and naturally integration of those systems on UAV physically.
#### What technologies do I need to learn?

I should learn all of those substances below. Additionally I should work on Python for data analysis and C++ for.

#### What libraries/frameworks can help me achieve this? 

As I learned, MAVLink named program is used to provide communication between UAV and GCS. So I need to learn it or similar programs to telemetry communication. 
Also I should learn ROS2 Iron software as data listener and talker on Ubuntu and if it is necessary the MAVROS package for integrating those two softwares communication with each other.

### **Sources**
- https://www.teknofest.org/tr/competitions/competition_report
- https://ardupilot.org/copter/docs/common-choosing-a-ground-station.html#overview
- https://www.rembeltech.com/enterprise/genel/pixhawk-the-cube-orange-standard-set-ads-b-carrier-board/
- https://en.wikipedia.org/wiki/UAV_ground_control_station?form=MG0AV3
- https://github.com/mavlink/mavros?form=MG0AV3