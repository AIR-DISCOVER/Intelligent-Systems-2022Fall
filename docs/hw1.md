# Intelligent Systems HW1

In this tutorial, `[Student ID]` should be replaced with your real student ID.

## What is the goal of this homework?

## What should I do to finish this homework?

1. Clone the HW1 project:
    ```shell
    git clone https://github.com/AIR-DISCOVER/IS2022Fall-hw1.git
    ```

2. Fill in the blanks of the file `course_ws/src/me_arm/script/MecArm.py`. Please pay attention to the block with `[TODO]` markers.

3. Run these lines to build a new Docker image.
    ```shell
    docker build . -t docker.discover-lab.com:55555/[Student ID]/client:hw1 
    ```

## How should I test the correctness of my answer?

> Start the simulation environment and start visualization

1. start simulation container

   Note that, the tag of the simulation environment is **hw1**.

   Start a new terminal and execute:

   ```shell
   docker network create net-sim
   docker run -dit --rm --name ros-master --network net-sim ros:noetic-ros-core-focal roscore
   docker run -it --rm --name sim-server --network net-sim -e ROS_MASTER_URI="http://ros-master:11311" --gpus all docker.discover-lab.com:55555/rmus-2022-fall/sim-headless:hw1
   ```

2. start visualization

   Start a new terminal and execute:

   ```shell
   xhost +
   docker run -dit --rm --name ros-gui --network net-sim -e ROS_MASTER_URI=http://ros-master:11311 -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 -v /tmp/.X11-unix:/tmp/.X11-unix docker.discover-lab.com:55555/rmus-2022-fall/ros-gui bash
   docker exec -dit ros-gui /opt/ros/noetic/env.sh rosrun image_view image_view image:=/third_rgb
   ```

3. start your own code.

   Note that, the tag of the baseline client docker is **hw1**.

   Start a new terminal and execute:

   ```shell
   docker run -it --rm -e ROS_MASTER_URI="http://ros-master:11311" --network net-sim --name hw1 docker.discover-lab.com:55555/[Student ID]/client:hw1
   ```

> Examine the correctness of your work

4. Start a shell and reset your EP to the right place:

   ```shell
   docker exec -it ros-master /opt/ros/noetic/env.sh rostopic pub /reset geometry_msgs/Point "x: 0.0
   y: 0.0
   z: 0.0"
   ```

5. Start a shell and listen to the joint states of the EP:

   ```shell
   docker exec -it ros-master /opt/ros/noetic/env.sh rostopic echo /joint_states
   ```

6. Start a shell and provide an arm position, and check out the joint states you solve in the second shell:

   ```shell
   docker exec -it ros-master /opt/ros/noetic/env.sh rostopic pub /arm_position geometry_msgs/Pose "position:
     x: X
     y: Y
     z: Z
   orientation:
     x: 0.0
     y: 0.0
     z: 0.0
     w: 0.0"
   ```

   Replace X, Y, Z with the actual arm position.


> Finish up
7. Stop the containers

   ```shell
   docker network rm net-sim
   docker stop hw1
   docker stop sim-server
   docker stop ros-master
   ```

## How do I submit my homework?

If your image is tested to be correct, push it to the cloud using following instructions.

Our judging system will automatically run your image and give you a score.
    
```shell
docker login docker.discover-lab.com:55555
# Input your Student ID and password
# The default password is [Student ID]ABCdef
# You can change it later in https://docker.discover-lab.com:55555
docker push docker.discover-lab.com:55555/[Student ID]/client:hw1
```