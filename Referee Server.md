Referee Server Research Assignment

1-How does the referee server work?
The referee server operates through an API that manages sending and receiving data. It is particularly suited to real-time performance requirements to ensure the accuracy and continuity of data.

 2-How is it used exactly?
 One of the main functions of the referee server is to provide data communication via APIs. RESTful or WebSocket APIs can be used for this purpose.If the main purpose of the server is for UAVs to regularly send data and receive general information of the competition, a RESTful API is sufficient.However, if constant live status updates need to be shared during the race, WebSocket is a more suitable choice.In addition, collecting, verifying and storing incoming data is of great importance in data management. JSON format offers a lightweight and readable solution for data communication and is frequently preferred in modern systems. Also, as stated in the communication document, it is stated that we should use JSON format.
 
 3-What technologies do I need to implement it?
 First of all, proficiency in Python and C++ programming languages, which are widely used on the ROS 2 platform, is important. One of the database management systems such as PostgreSQL or MongoDB can be used to store data securely and regularly. In API development options, web technologies such as Flask or FastAPI, which offer lightweight and fast solutions, can be preferred. Additionally, the basic principles of ROS 2's provision of DDS (Data Distribution Service), which enables data transfer and communication, must be understood. In addition, to meet real-time data processing and control conditions, knowledge about real-time operating systems (RTOS) and the capacity of ROS 2 in this field has a critical closing feature.
 
 4-What technologies do I need to learn?
 Effective use of APIs is important in the development of the referee server. Therefore, learning technologies such as RESTful APIs and WebSocket is a basic requirement to ensure data communication.In addition, gaining experience with SQL database systems to store and manage data correctly will increase the reliability and functionality of the server.

 5-What libraries/frameworks can help me achieve this?
To use the core functionality of ROS 2, rclpy (Python) and rclcpp (C++) libraries will help with processes such as node creation, messaging and service calls.On the web development side, flask and fastapi can be preferred to create APIs.For database operations, SQLAlchemy can be used, which is compatible with PostgreSQL or SQLite.In the testing and simulation processes of the referee server, Gazebo can be used to perform physical simulations of UAVs and RViz can be used to visualize data and messages.

Sources

https://cdn.teknofest.org/media/upload/userFormUpload/Savasan_IHA_Yarismasi_v1.1_yeVCZ.pdf
https://cdn.teknofest.org/media/upload/userFormUpload/HABERLE%C5%9EME_D%C3%96K%C3%9CMANI_BtlRO.pdf
https://www.techtarget.com/searchapparchitecture/definition/RESTful-API
https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API
https://learn.microsoft.com/en-us/sql/relational-databases/json/json-data-sql-server?view=sql-server-ver16
https://wiki.ros.org/rosbridge_suite
