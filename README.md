# ros-jazzy-humble-cyclonedds

This is a modified version of [ROS Humble CycloneDDS](https://github.com/16stelter/rmw_cyclonedds) that establishes compatibility between ROS2 Humble and ROS2 Jazzy. Basically, it just adds some missing functions from Jazzy to the Humble version.

## DISCLAIMER

This is a very quick implementation and I take absolutely no responsibility for anything you do with this. Everything I have tested works between Jazzy and Humble in both directions, as well as between devices on the same Distribution. However I cannot guarantee that I haven't missed anything or that different devices won't behave weirdly with this. Use at your own risk. If you find any issues or have some improvements, feel free to open a pull request or issue. Also note that this is a fork of Humble, all improvements introduced with Jazzy will not be included here.

## Setup

### Jazzy Devices

On Jazzy devices, you will need to make a few modifications. First start by cloning this repository to the src folder of your ros2 workspace:

```git clone git@github.com:16stelter/ros-jazzy-humble-cyclonedds.git```

Next, install the included .deb file to replace ```ros-jazzy-rmw-dds-common``` without removing all packages that depend on it. The .deb does nothing except having the same name as the actual package. If you don't want to install a random person's .deb file, see below how to create your own dummy package.

```
sudo dpkg -i ros-jazzy-rmw-dds-common_0.0.1_all.deb
```

Now build the rmw packages and source your workspace.

```
colcon build --packages-up-to rmw_cyclonedds_cpp
source ./install/setup.bash
```

Finally, set the RMW_IMPLEMENTATION environment variable

```
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
```

### Humble Devices

Humble devices are easier to set up. Simply install the official cyclonedds packages and set the environment variable:

```
sudo apt install ros-humble-cyclone ros-humble-rmw-dds-common
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
```

### Creating a dummy .deb file

The uncompiled .deb is also included in the dummy-package folder. Simply navigate to the folder and use Equivs to generate the .deb, then install:

```
sudo apt install equivs
equivs-build ros-jazzy-rmw-dds-common
sudo dpkg -i ros-jazzy-rmw-dds-common_0.0.1_all.deb
```

Feel free to make your own modifications to the build file before building.

## Testing

The easiest way to test whether the installation was successful is to start a demo talker node on one device and a demo listener node on the other device.

```
ros2 run demo_nodes_cpp talker
ros2 run demo_nodes_cpp listener
```

If both nodes can communicate, the setup was successful.

I have tested this software on a Ubuntu 24.04 laptop with Jazzy and a Ubuntu 22.05 Server Raspberry Pi with Humble. I have verified that both the talker/listener demo and the service server/client demo work in both directions, as well as the ros2 node, service, action and topic commands. If you encounter any problems, please open an issue.

## Known Issues

When first setting up this modification, old rmw nodes are not always closed correctly. This may cause weird behaviours such as commands getting stuck indefinitely or reporting message types to be unreadable. If this occurs, simply kill all running rmw nodes, e.g. using htop.
