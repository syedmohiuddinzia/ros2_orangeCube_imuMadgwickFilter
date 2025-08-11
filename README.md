# Using Madgwick Filter on IMU data of Orange Cube using ROS2

This guide explains how to filter and visualize IMU data published on `/mavros/imu/data` using the `imu_filter_madgwick` node in ROS 2.

![gif](https://github.com/syedmohiuddinzia/ros2_orangeCube_imuMadgwickFilter/blob/main/pic/gif_.gif)

---
### Note:

This project builds upon the foundational setup detailed in [ros2_orangeCube_integration](https://github.com/syedmohiuddinzia/ros2_orangeCube_integration).
Please make sure to follow the integration steps there first to get your Cube Orange connected with ROS 2 and MAVROS properly before using this IMU Madgwick filter package.

---

### Step 1: Install `imu_filter_madgwick`

If not already installed, install the package:

```bash
sudo apt update
```
```bash
sudo apt install ros-humble-imu-filter-madgwick
```
### Step 2: Verify /mavros/imu/data topic
Make sure your IMU data is publishing on /mavros/imu/data:
```bash
ros2 topic echo /mavros/imu/data
```
![1](https://github.com/syedmohiuddinzia/ros2_orangeCube_imuMadgwickFilter/blob/main/pic/1.png)
You should see IMU data messages with orientation, angular velocity, and linear acceleration fields.

### Step 3: Run imu_filter_madgwick_node with proper remapping and parameters
Run the filter node, remapping its expected input topic (/imu/data_raw) to /mavros/imu/data, and disable magnetometer usage:
```bash
ros2 run imu_filter_madgwick imu_filter_madgwick_node --ros-args \
  -r imu/data_raw:=/mavros/imu/data \
  -p use_mag:=false
```
![2](https://github.com/syedmohiuddinzia/ros2_orangeCube_imuMadgwickFilter/blob/main/pic/2.png)
This will publish filtered IMU data on /imu/data.

### Step 4: Verify filtered IMU output
Check the filtered output:
```bash
ros2 topic echo /imu/data
```
![3](https://github.com/syedmohiuddinzia/ros2_orangeCube_imuMadgwickFilter/blob/main/pic/3.png)

### Step 5: Visualize filtered IMU data in rviz2
1- Start rviz2:
```bash
rviz2
```
2- In rviz2:
- Click Add in the Displays panel.
- Choose IMU display type.
- Set the topic to /imu/data.
- Set the fixed frame (usually base_link or the frame used by your IMU messages).
3- You should now see a 3D visualization of IMU orientation and angular velocity.
![4](https://github.com/syedmohiuddinzia/ros2_orangeCube_imuMadgwickFilter/blob/main/pic/4.png)

### Optional: Create a launch file
You can create a ROS 2 launch file to automate the above. Example:
```xml
<launch>
  <node pkg="imu_filter_madgwick" exec="imu_filter_madgwick_node" name="imu_filter_madgwick_node" output="screen">
    <param name="use_mag" value="false"/>
    <remap from="imu/data_raw" to="/mavros/imu/data"/>
  </node>
</launch>
```
