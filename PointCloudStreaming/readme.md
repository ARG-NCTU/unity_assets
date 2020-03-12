# PointClound Streaming from ROS

This asset allows to render PointCloud2 from ROS into Unity

### Dependency
- ROS-sharp. 
- An rgb-d camera or lidar should be running in a ROS-running PC, which is connected to a Unity-running PC by using a LAN cable.  

### Instruction 
1. Copy this folder to "Assets" of your project.
2. Put `enableOpenGL.cs` on Main Camera object.
3. Put `PointCloudSubscriber.cs` on RosConnector object (Assuming that you already have ROSConnector). Then, set the name of pointcloud topic according to your ROS PC. 
3. Make a new game object, and put `PointCloudRenderer.cs`. 
4. Drag and drop the RosConnector object to `Subscriber`, and set `Point Size`. 
5. Make a new game object, which will be used to adjust the pointclouds' position, and place it into `Offset` in `PointCloudRenderer.cs`. 
6. Maybe you need to start Unity with `-force-opengl` option via command. 

### Further Details

#### File Explanation
1. enableOpenGL.cs
This script loads the openGL and uses glEnable to give us access to the `GL_VERTEX_PROGRAM_POINT_SIZE` which allows us to change the size of points rendered in the cloud.

2. PointCloudRenderer.cs
This script takes an array for the colour and and an array for the position of every point. 
Positions in Unity use a special class called Vector3 which define the x, y, z coords of a position. 
Colours in Unity are floating point values for RGB (and Alpha) between 0 and 1. 

The public float PointSize will allow you to change the size of all rendered points in the cloud, this can be changed
before and during runtime.

3. PointCloudShader.shader
This is a custom shader script which implements a float property called _PointSize. You don't need to do anything with this script, PLEASE DO NOT CHANGE ITS NAME. The pointCloud script automatically creates a material and assigns this custom shader to it
for rendering the point cloud.

4. Editor > GraphicsSettingsEditor.cs
This is an editor script which ensures the application is using OpenGL. If you attempt to change the graphics API in Project Settings > Player > Other Settings this script will automatically change the API back to OpenGL and warn you that the editor needs to be restarted.
You don't need to do anything with this script. The Point Size functionality depends on OpenGL being used, however, if you must use Direct3D or Vulkan then delete this script and good luck...

#### Understanding PointCloud2 data 

[PointCloud2](http://docs.ros.org/melodic/api/sensor_msgs/html/msg/PointCloud2.html) has data field where there are the information (e.g. position, intensity, rgb, etc.) of each point. It may look like [this](https://answers.ros.org/question/58112/how-can-i-save-a-msg-to-a-file/). 

In this example, each point has 32 data field (as shown in `point_step`), where its position is represented by `0`, `4`, and `8` th element (as shown in `offset`). Likewise, the intensity is shown from `16`th element. This offset information is IMPORTANT to extract any necessary information from a Unity script. 

For your project, the data field may not look the same. So, I recommend you to investigate your data first by executing, for example, 

        rostopic echo -p /camera/depth/points > pcl.txt

Then, open the txt file, you will see how the data field look like. 
        