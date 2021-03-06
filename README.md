# DRL Landing for Bebop 2 under Parrot Sphinx Software
This is the repository for making autonomous landing on moving platform based on picture from Bebop 2 RGB camera. I used [drl-landing] package and implemented it under **Parrot Sphinx** software and **bebop_autonomy** driver. I also implemented avoiding collision betweeen two drones, where first is the leader and second (avoiding drone) is the follower.

## Some results of simulation and real test
In Gazebo simulator it looks like this:

![Simulation test](https://github.com/m-milena/bebop2_drl_landing/blob/master/Images/aruco_detection.png)

Real test was made with static platform:

![Real test](https://github.com/m-milena/bebop2_drl_landing/blob/master/Images/real_test.png)

And there are videos from simulation and real test:

<a href="https://youtu.be/e1Hn_gFjMF0
" target="_blank"><img src="http://img.youtube.com/vi/e1Hn_gFjMF0/0.jpg" 
alt="Simulation test" width="240" height="180" border="10" /></a>
<a href="https://youtu.be/Tf4SAZeZxFg
" target="_blank"><img src="http://img.youtube.com/vi/Tf4SAZeZxFg/0.jpg" 
alt="Real test" width="240" height="180" border="10" /></a>

## What you should have if you would like to use it:
- **install ROS** (I was working under **ROS Kinetic** on Ubuntu 16.04 LTS) - [ROS Kinetic Installation]. It is possible to run it under ROS Melodic on Ubuntu 18.04, but you do it at your own risk.
- **install Gazebo** (Probably it is install automatically with ROS but check it out)
- **install Parrot Sphinx software** - [Parrot Sphinx install]
- **install teleop_twist_keyboard** - for test purpose, I write keboard control for lider and follower drone, which include also camera control. You don't have to install it, if you don't wanna use it. 

```console
$ sudo apt-get install ros-kinetic-teleop-twist-keyboard
```

- **install OpenCV and ArUco** using *.sh files from [opencv_aruco_install]
Now you have to make your own catkin workspace [create workspace] and install:
- **bebop_autonomy** - [bebop_autonomy install]
- **dronemsgsros** - [dronemsgsros repository]
- **nav_msgs** - [nav_msgs install]
- **control_toolbox and control_msgs**:
```console
$ sudo apt-get install ros-kinetic-control-toolbox ros-kinetic-control-msgs
```

- **gym** - [gym install]
- **drl-landing** - [drl-landing install] - install original drl-landing repository, then you will make some modifications with it.

## What this repository includes and what modifies you have to make to run the project:
This repository includes:
- **bebop_autonomy_modification** - is the folder, which includes modifications with bebop_autonomy driver. I added two launch files: for leader and follower. You have to paste them to *../your_workspace/src/bebop_autonomy/bebop_driver/launch*.
- **drone_files** - this folder includes leader and follower *.drone files, which Parrot Sphinx use to launch the drone in simulation. You have to paste them to */opt/parrot-sphinx/usr/share/sphinx/drones*. If you would like to launch two drones in one simulation, you have to download elder version of firmware. You can get it from [Firmware version]. Download **Bebop 2 - 4.4.2** and in both files change the firmware path to your own. If you are going to run only one drone, copy firmware path from bebop2.drone file.
- **Plugins** includes plugins to move platform in many ways. Paste them anywhere.
- **Gazebo_3Dmodels** includes 3D models of platform and room from AeroLab on Poznan University of Technology. Paste them to *~/.gazebo/models*.
- **Worlds** - includes two Gazebo worlds: one empty world with platform and second room_world also with platform. You can paste them to */opt/parrot-sphinx/usr/share/sphinx/worlds*.
- **drl-landing_modified** includes modified version of [drl-landing]. You have to paste content of Environment folder to *../your_workspace/src/drl-landing/code-rl-environment-gazebo/rl_envs* and overwrite existing files. Then 
- **bebop_dronemsgsros** - paste this folder to *../your_workspace/src*
- **bebop_keyboard** -also paste to *../your_workspace/src*. It includes keboard control to both drones and their camera
- **collision_avoid** - paste to *../your_workspace/src*.
Now you can compile all workspace:
```console
$ cd ../your_workspace
$ catkin build
```

## How to run it?
First run Parrot Sphinx software with one drone and in example world. I choose empty world with platform:
```console
Run firmware:
$ sudo firmwared
Open new terminal or CTRL+SHIFT+T and go to the folder with saved plugin from [Plugins]:
$ cd ../autolanding_low_speed_plugin
Run Sphinx software with empty world:
$ GAZEBO_PLUGIN_PATH=`pwd`/build:$GAZEBO_PLUGIN_PATH sphinx --log-level=dbg /opt/parrot-sphinx/usr/share/sphinx/worlds/empty_with_platform.world /opt/parrot-sphinx/usr/share/sphinx/drones/bebop_leader.drone::with_front_cam=true
```

Wait until drone appeared and run bebop_autonomy driver:

```console
$ cd ~/.../your_workspace
$ source devel/setup.bash
$ roslaunch bebop_driver bebop_leader_node.launch
```
Then:

```console
CTRL+SHIFT+T
$ rosrun bebop_dronemsgsros bebop_dronemsgsros
CTRL+SHIFT+T
$ roslaunch RlEnvironmentGazeboRos rlEnvironmentGazeboROSLandingWithRPdYdAPE_marker_real.launch
CTRL+SHIFT+T
$ roslaunch rl_agent ddpg_agent.launch
```
If you are going to run keyboard control:
```console
$ cd ../your_workspace
$ rosrun bebop_keyboard bebop_leader_keyboard.launch
```

to be continued


[drl-landing]:<https://github.com/alejodosr/drl-landing>
[ROS Kinetic Installation]:<http://wiki.ros.org/kinetic/Installation>
[Parrot Sphinx install]:<https://developer.parrot.com/docs/sphinx/installation.html>
[opencv_aruco_install]:<https://github.com/m-milena/bebop2_drl_landing/tree/master/opencv_aruco_install>
[Plugins]:<https://github.com/m-milena/bebop2_drl_landing/tree/master/Plugins>
[create workspace]:<http://wiki.ros.org/catkin/Tutorials/create_a_workspace>
[bebop_autonomy install]:<https://bebop-autonomy.readthedocs.io/en/latest/installation.html>
[dronemsgsros install]:<https://github.com/Ahrovan/dronemsgsros>
[nav_msgs install]:<http://wiki.ros.org/nav_msgs>
[gym install]:<https://github.com/openai/gym>
[Firmware version]:<http://plf.parrot.com/sphinx/firmwares/index.html>
[drl-landing install]:<https://github.com/alejodosr/drl-landing>
