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
echo "export SEGWAY_INTERFACE_ADDRESS=10.66.171.1" >> ~/.bashrc
echo "export SEGWAY_IP_ADDRESS=10.66.171.5" >> ~/.bashrc
echo "export SEGWAY_IP_PORT_NUM=8080" >> ~/.bashrc
echo "export SEGWAY_BASE_PLATFORM=RMP_110" >> ~/.bashrc
echo "export SEGWAY_PLATFORM_NAME=RMP_110" >> ~/.bashrc
```

## Usage

### Navigation

To launch bwi and all packages required for navigation at CSU use
```
$ roslaunch bwi_launch segbot_csu.launch
```

You likely will want to open up rviz to give a pose estimate or simple navigation goals.
```
$ rosrun segbot_navigation rviz_runner
```

If you need to drive the segbot with a controller use the bwi_joystick_teleop package. Double check that the joystick is in X mode and not D mode or it will not work as expected. You can check this by looking at the physical switch on the joystick. 
```
$ roslaunch bwi_joystick_teleop joystick_teleop.launch
```

### Making a map with the segbot

You'll need to turn on the base segway drivers followed by gmapping and rviz.
```
$ roslaunch segbot_bringup segbot_csu.launch
$ roslaunch segbot_navigation gmapping_csu.launch
$ rosrun rviz rviz
```

You can see your map being created in real time by selecting the map topic in rviz. When you are done you can save your map with the map_server package.
```
$ rosrun map_server map_saver -f ~/<map_name>
```

That will save a map in your selected directory.

### Adding a map to the bwi system.

Adding a map to bwi takes multiple steps. First you need to put the maps in the correct directory and update the world file accordingly. Then, you need to logically mark the map with locations, doors, and objects. Finally, you need to update asp to reflect the marked locations. For this example I am adding maps for an imaginary 5th floor.

The maps at CSU live at bwi_common/utexas_gdc/maps/real/csu/. Navigate there and copy the map and yaml file into a folder named 5. Note that I renamed the map and yaml from myMap to 5. Make sure the yaml file reflects this change.
```
$ roscd utexas_gdc/maps/real/csu
$ mkdir 5
$ cp ~/myMap.yaml ./5/5.yaml
$ cp ~/myMap.pgm ./5/5.pgm
```

We need to use the logical marker tool to mark locations and doors. Every part of the map should be marked with a location. For a new floor you need at a minimum 2 locations (one for an elevator and one for the rest of the floor) and 1 door connecting them. Ideally there would be more than that. To run the logical marker use:
```
$ rosrun bwi_planning_common logical_marker _map_file:=5.yaml _data_directory:=./
```
Remember to use your own map file and the proper data directory.

Now we need to update the world file to support a new floor. CSU's multimap yaml file is in utexas_gdc/maps/real/multimap/csu_multimap.yaml.

Lastly we need to update the asp to reflect the changes we made with the logical marker. The asp for cleveland state is in bwi_kr_execution/domain. If you used the logical marker tool to update an already exsiting map, you'll need to edit navigation_facts.asp. If you added a new floor or additional elevators you need to edit elevators_facts.asp in addition to navigation_facts.asp. Make sure every location/door/object has corrisponding asp.
