Generating and Rendering A High-Fidelity Virtual World out of Vehicle Sensor Data
=======

Abstract
---------------
In the automotive world, there is an emerging need to test highly automated driving systems in virtual 3D worlds. The motivation for virtual testing is that simulation can reduce costs and avoid dangers that accompany real-world tests, and it offers the motor vehicle tester sufficient flexibility to explore a wider range of test conditions without being hampered by practical constraints associated with real tests. Currently there is no automated way to generate high fidelity virtual environments for vehicle testing or re-simulation with highly automated driving systems. This dissertation aims to develop software to accurately generate and render a virtual world out of vehicle sensor data, and to analyse the quality and accuracy of the generated world.

This dissertation met these research aims through an extensive study of relevant literature surrounding automated world generation and rendering as well existing simulation environments for highly automated driving. Coupled with a state-of-the-art prototype vehicle setup, a software application was developed that generated and rendered a voxelized virtual world from real-world sensory data. The work herein produced several research questions related to evaluating the geometric and textural accuracy of the world and what parameters affected that accuracy. After performing various tests derived from the research questions, it was found that low vehicle odometry accuracy affected the accuracy generated world. For example, the low vehicle odometry accuracy caused by high speed vehicle data resulted in motion blur and subsequently point distortion among the generated world. It was also found that, in addition to vehicle speed, other parameters to affect the accuracy of the world included: LiDAR sensor resolution, voxel size, and voxel texturing quality.

Overview
---------------
- Develop research questions: What is needed to build a high fidelity rendered world out of automotive sensory data? What are the parameters influencing the quality of the built virtual world?
- Examine existing simulators including Microsoft AirSim, CARLA, Unity SimViz and Udacity Simulator and identify where this research fits in amongst them. 
- Determine the required tech stack: a prototype vehicle with ROS (C++) on Ubuntu managing various hardware components including radar, LiDAR and optical sensors and a DGPS system; and a windows development machine running Unity (C#, HLSL) which interfaces with and receives data from ROS on the prototype vehicle.
- Write data processing algorithms to enable the received data to be used within Unity including WGS84 to UTM10 coordinate system transformations, ROS to Unity axis swap, and point cloud transformations from local space through sensor space to world space. 
- Design acceleration data structures that the processed point cloud data can be stored in: OctoMap, An Efficient Probabilistic 3D Mapping Framework Based on Octrees. This data structure allows for occupied, unoccupied and free space to be mapped and has a small on-disk footprint as it does not explicitly store individual node positions, rather it stores parent-child relationships.
- Apply probabilistic occupancy to improve the map accuracy. It works by using an aggregation of data to determine if a node is being occupied or not. Instead of the occupancy state being binary (as in free or occupied), the occupancy state is more of an occupancy likeliness percentage.
- Effectively store and load the serialized data on disk so that only the relevant areas are loaded for rendering or for when new data is contributed to the world. This is achived through the use of multiple OctoMaps on disk, with each OctoMap covering an ~11m cubed area, calculated by truncating WGS84 LatLong coordinates to 4 decimal places. A MasterFile is used to keep track of the file locations of each individual OctoMapNode, which can then be dynamically loaded and unloaded depending on the current requirements of the application.  
- Render and Texture the World in the most optimal way. OctoMaps are loaded into memory based on the applcations current requirements. Individual nodes from each OctoMap and selected to be rendered depending on if they are in the cameras view frustum, calculated on the GPU through a compute shader. The selected nodes are then rendered through indirect GPU Instancing. The fragment shader on the voxels material uses instance specfic information to texture each pixel on each voxel using the texture data from the optical cameras on the prototype vehicle. The first step in the fragment program is to convert the world position of the pixel to a coordinate system which is fixed with respect to the camera that can see of the current voxel. This is done by transforming the world position of the pixel by the inverse of the cameras current pose (which is the extrinsic matrix), and from there, dividing ​X and ​Y​ by ​Z​ results in a 2D coordinate for the pixel. From there the 2D coordinate needs to be corrected for distortions and finally, the intrinsic matrices are applied to the ​X​ and ​Y​ positions, which returns a UV position which is a 2D coordinate on the texture.

Artefact Screenshots
---------------

![Image](https://alex-scott.co.uk/img/portfolio/MScThesis1.png)
![Image](https://alex-scott.co.uk/img/portfolio/MScThesis2.png)
![Image](https://alex-scott.co.uk/img/portfolio/MScThesis3.png)
![Image](https://alex-scott.co.uk/img/portfolio/MScThesis4.png)
![Image](https://alex-scott.co.uk/img/portfolio/MScThesis5.png)
![Image](https://alex-scott.co.uk/img/portfolio/MScThesis6.png)

