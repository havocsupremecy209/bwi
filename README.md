# bwi

This repository contains top-level ROS packages for the Building Wide
Intelligence (BWI) project of the University of Texas at Austin
Computer Science Department.

## BWI Repository Hierarchy

Packages contained in various released BWI repostories may depend on
other packages at the same or lower levels.  Dependencies on packages
from higher-level repositories are not permitted.

From top to bottom, the released repositories are:

 * [bwi](http://wiki.ros.org/bwi)
 * [segbot](http://wiki.ros.org/segbot)
 * [bwi_common](http://wiki.ros.org/bwi_common)

## Installation

### From Source

You can install all the BWI components normally built from source on
either ROS Hydro or Indigo.

The Hydro version is now *deprecated*. It may work, but is no longer actively 
maintained.

First, [install ROS Hydro](http://wiki.ros.org/hydro/Installation/Ubuntu), 
[Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu), or
[Kinetic](http://wiki.ros.org/indigo/Installation/Ubuntu).

The Kinetic version is only supported on Ubuntu Xenial, and is
probably not fully functional, yet.

Then, make sure the ROS_DISTRO environment variable is set correctly:

```
echo $ROS_DISTRO
```

It may already be.  If not, issue the appropriate one of these two
shell commands:

```
$ export ROS_DISTRO=hydro
```
or
```
$ export ROS_DISTRO=indigo
```

Next, clone the source repositories:
```
$ source /opt/ros/$ROS_DISTRO/setup.bash
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws
$ wstool init src https://raw.githubusercontent.com/csu-air-group/bwi/master/rosinstall/$ROS_DISTRO.rosinstall
```

Install all dependencies:
```
$ rosdep install --from-paths src --ignore-src --rosdistro $ROS_DISTRO -y
```

Then, build everything:
```
$ catkin_make
$ source devel/setup.bash
```

### For Version 3 Robot

To use this code on the Version 3 Segway Robot, one must also define
some enviroment variables. The shell commands below will add them to your bashrc. Double check that the interface address and ip address are correct. You can check this by using the command:
```
$ ifconfig
```

```
echo "export SEGWAY_INTERFACE_ADDRESS=10.66.171.1" >> ~/.bashrc
echo "export SEGWAY_IP_ADDRESS=10.66.171.5" >> ~/.bashrc
echo "export SEGWAY_IP_PORT_NUM=8080" >> ~/.bashrc
echo "export SEGWAY_BASE_PLATFORM=RMP_110" >> ~/.bashrc
echo "export SEGWAY_PLATFORM_NAME=RMP_110" >> ~/.bashrc
```

### From Binary Packages
NOTE: Binary Packages are from ut, so do not expect them to work on the csu segbot without some tinkering.

You may install the latest binary release, when available, from the
ROS package repository.  These will not usually be the latest versions:

```
$ sudo apt-get install ros-$ROS_DISTRO-bwi-desktop-full
```
