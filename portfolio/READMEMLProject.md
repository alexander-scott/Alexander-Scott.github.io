Magic Leap Project
=======

Overview
---------------
- Determine the required tech stack: a prototype vehicle with ROS (C++) on Ubuntu managing various hardware components including radar, LiDAR and optical sensors and a DGPS systen; a Magic Leap (OpenGLES 3.1) headset which can run applications built by Unity (C#,HLSL). 
- Write data processing algorithms to enable the received data to be used within Unity and to convert it to world space. The data received is an array of 3D objects with varying width, height and depths and different 2D lateral plane positions and a horizontal rotation. These cubes represent the dynamic objects detected by the cars sensors including cars, trucks, bicycles and pedestrians. The transformations required include WGS84 to UTM10 coordinate system transformations, ROS to Unity axis swap, and object transformations from local space to vehicle space. 
- Design a way to convert data from vehicle space to headset space. This was achieved by using a reference point that existed in both spaces: which was a small area with an image that the user would face. This point was identifed in vehicle space by measuring the distance to it from the vehicle space origin (the vehicle rear axis) and hard-coding that value. The point was identified in headset space by using the Magic Leap's image tracking functionality, which is able to track images and calculate the distance from the headset to the image. By using the reference point, objects can be transformed from vehicle space to headset space by multiplying the objects by the inverse of the vehicle space reference point matrix to set the object's space origin to the reference point. Then the objects are multipled by the headset space reference point matrix (or where the tracked image is in relation to the headset) to set the object's space origin to the headset.
- Render the objects through the headset in the most optimal way: indirect GPU Instancing is used to render the objects which allows the previously mentioned transformation calculations to be performed on a GPU through an instance shader. 


Discovered Issues
---------------
- When the vehicle turns too quickly, the headset loses its position in the world.
- Unity 2018.1 IL2CPP doesn’t support TLS 1.2 for secure websocket connections.
- A ROS node was transformed around the vehicles wheel-based odometry instead of the vehicles rear-axis, which required an additional calculation to be done to negate the current wheel odometry.

