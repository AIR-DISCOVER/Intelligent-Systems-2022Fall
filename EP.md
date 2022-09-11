# RoboMaster EP 介绍

![RoboMasterEP](./assets/RoboMasterEP.gif)

The [RoboMaster EP](https://www.dji.com/cn/robomaster-ep) is an autonomous vehicle platform equipped with a 4 axis palletizing robot arm and an encircling gripper for flexible gripping action, inspired from DJI's annual RoboMaster robotics competition. It provides Mecanum wheel omnidirectional movement, fast wireless data link system including realtime video stream, and open sdk for further development and programming.

In order to match the theme of the course, we equip the RoboMaster EP with an external computing platform (NUC), as well as additional sensors including **onboard lidar** and **RGB-D cameras** for the purposes of better perception of the environment.

An external computing platform is also prepared to communicate with EPs through RoboMaster EP SDK. This platform is dedicated to run real applications in both simulated and real environments.

* [RoboMaster EP 介绍](#robomaster-ep-介绍)
   * [NUC](#nuc)
   * [Sensors](#sensors)
      * [Lidar](#lidar)
      * [IMU](#imu)
      * [RGB &amp; Depth Camera](#rgb--depth-camera)
      * [Odometer](#odometer)
   * [Accurators](#accurators)
   * [ROS Interfaces](#ros-interfaces)
      * [Sensor Topics](#sensor-topics)
      * [Accurator Topics](#accurator-topics)
      * [Other Topics](#other-topics)

## NUC

The specification of the equipped NUC:

| Type | NUC11PAHI7           |
| :----: | :--------------------: |
| CPU  | i7-1165G7 (2.8GHz, 8 Cores) |
| RAM  | 8GB                  |
| SSD  | 256G                 |

## Sensors

The specification for the equipped sensors:

### Lidar

[SlamTech Rplidar A2](https://www.slamtec.com/cn/Lidar/A2)

|Key|Value|
|:-:|:-:|
|Scan Rate|$10hz$|
|Sample Rate|$16Khz$|
|Distance Range|$[10m,25m]$ [TODO check]|
|Mimimal Operating Range|$0.2m$|

### IMU

[HiPNUC Hi226 6-axis IMU/VRU](https://www.hipnuc.com/product_hi226.html)

|                       Key                        |  Value   |
| :----------------------------------------------: | :------: |
|                    Frequency                     |  $30hz$  |
|           Static Roll and Pitch Errors           |  $0.8°$  |
|     Static Roll and Pitch Angles Error Bound     |  $2.5°$  |
|             Bias Error of Gyroscope              | $<10°/h$ |
| Heading Angle Error When Moving (in 6-axis mode) |  $<10°$  |

### RGB & Depth Camera

[Intel Realsense D435i](https://www.intelrealsense.com/zh-hans/depth-camera-d435i/)

|    Key     |     Value      |
| :--------: | :------------: |
| Frequency  |     $30hz$     |
| Resolution | $848\times480$ |
|    FOV     | $69°\times42°$ |

### Odometer

[RoboMaster SDK](https://github.com/dji-sdk/robomaster-sdk)

|    Key    | Value  |
| :-------: | :----: |
| Frequency | $10hz$ |

## Accurators

| Actuator                 | Recommend Range                                              |
| :------------------------: | :------------------------------------------------------------: |
| chassis velocity control | $0.1m/s\leq\|v_x\|\leq0.5m/s$ <br>$0.1m/s\leq\|v_y\|\leq0.5m/s$ <br>$0.01rad/s\leq\|v_{th}\|\leq0.5rad/s$ |
| chassis position control | $\|x\| \geq 0.1m$ <br>$\|y\| \geq 0.1m$ <br>$\|theta\| \geq 0.1rad$ |
| arm end position control | while $0.09\leq x \leq 0.18$, should keep $y\ge 0.08$ <br>while $x>0.18$, should keep $y\ge -0.02$ |
| gripper control          | $x=1$ close gripper <br>$x=0$ open gripper                   |

## ROS Interfaces

### Sensor Topics

|Name|Type|Description|
|:-|:-|:-|
|`/ep/odom`|`nav_msgs/Odometry`|Odometer data, including robot pose and speed information, are obtained by DJI master control.|
|`/rplidar/scan`|`sensor_msgs/LaserScan`|The two-dimensional lidar scanning data, including scene scanning information, is acquired by lidar because the occlusion range of the robot body includes 270° in front of the robot.|
|`/imu/data_raw`|`sensor_msgs/LaserScan`|IMU sensor data, including rotation, velocity, and acceleration information, are collected by the IMU.|
|`/camera/color/camera_info`|`sensor_msgs/CameraInfo`|RGB color image camera intrinsic parameter information.|
|`/camera/color/image_raw`|`sensor_msgs/Image`|RGB color image data, acquired by Realsense.|
|`/camera/aligned_depth_to_color/camera_info`|`sensor_msgs/CameraInfo`|Depth camera information.|
|`/camera/aligned_depth_to_color/image_raw`|`sensor_msgs/Image`|Depth image data, acquired by Realsense and aligned to RGB color images.|

### Accurator Topics

|Name|Type|Description|
|:-|:-|:-|
|`/cmd_vel`|`geometry_msgs/Twist`|Velocity command for the EP chassis.<br />Recommended ranges:<br />$$0.1m/s\leq|v_x|\leq0.35m/s$$<br />$$0.1m/s\leq|v_y|\leq0.35m/s$$<br />$$0.1rad/s\leq|v_{th}|\leq0.5rad/s$$|
|`/cmd_position`|`geometry_msgs/Twist`|Position command for the EP chassis.<br />Recommended ranges:<br />$$0.1m\leq|x|$$<br />$$0.1m\leq|y|$$<br />$$0.1rad\leq|theta|$$|
|`/arm_position`|`geometry_msgs/Pose`|Position control command for the Robotic arm.<br />Available ranges:<br/>$0.09\leq x \leq 0.18$:  $y\ge 0.08$<br />$x>0.18$:  $y\ge -0.02$|
|`/arm_gripper`|`geometry_msgs/Point`|The commands for gripper.<br />Close: $x=1$<br />Open: $x=0$|

Examples:

```shell
# Move EP forward for 0.1m
rostopic pub -1 /cmd_position geometry_msgs/Twist \
"linear:
  x: 0.1
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 0.0"
```

```shell
# Move the end of the robotic arm to x=0.09, y=0.1
rostopic pub -1 /arm_position geometry_msgs/Pose \
"position:
  x: 0.09
  y: 0.1
  z: 0.0
orientation:
  x: 0.0
  y: 0.0
  z: 0.0
  w: 0.0"
```

```shell
# the command issued now is tp close the gripper
rostopic pub -1 /arm_gripper geometry_msgs/Point \
"x: 1.0
y: 0.0
z: 0.0" 
```

### Other Topics

|Name|Type|Description|
|:-|:-|:-|
|`/pose/cube_N`|`geometry_msgs/Pose`|The position of the ores.<br />The mineral ore numbered with `N` start from 1 to 5, e.g. `/pose/cube_1`<br />|
|`/position/target_N`|`geometry_msgs/Point`|The position of the exchange stations.<br />The Exchange Station numbered with `N`, ordering from left to right as 1/2/3.<br /> For example, `/pose/target_1` corresponds to the place information of the leftmost Exchange Station:|
|`/judgement/exchange_markers`|`std_msgs/String`|The marker digits of the exchange stations in the format of `data: "3, 5, 2"`<br / >For debug only.|
|`/judgement/markers_time`|`std_msgs/String`|The time of placing the mineral ore in the right exchange station.<br />Defined as:<br /> `nan`: waiting for the client<br />`0`: client EP robot has moved but the ore is not placed in exchange station<br />`[float]`: elapsed time of the ore get placed <br />`None`: hit the exchange station, or the ores are not put in the right exchange station.<br />For debug only.|