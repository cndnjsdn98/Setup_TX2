# Setup ROS and Catkin Workspace Environment for PX4 Autopilot and Agilicious
Step by Step instructions for setting up a ROS and Catkin_ws environment on Jetson TX2 for PX4 offboard flights.

## Install required Packages
Following instructions are for installing dependencies such as MAVROS and MAVLINK for communicating with the FCU.
```
sudo apt-get install ros-melodic-mavros ros-melodic-mavros-extras -y
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
chmod +x ./install_geographiclib_datasets.sh
sudo ./install_geographiclib_datasets.sh
rm ./install_geographiclib_datasets.sh
```


1. Required dependencies
```
sudo apt-get update
sudo apt-get install -y libgoogle-glog-dev \
                        protobuf-compiler \
                        ros-$ROS_DISTRO-octomap-msgs \
                        ros-$ROS_DISTRO-octomap-ros \
                        ros-$ROS_DISTRO-joy
```
```
sudo apt-get install -y libopencv-dev \
                        ros-${ROS_DISTRO}-mavros \
                        ros-${ROS_DISTRO}-mavros-extra \
                        ros-${ROS_DISTRO}-mavros \
                        ros-${ROS_DISTRO}-mavros-extras \
                        ros-${ROS_DISTRO}-mavros-msgs
```
1. Install required packages 
2. Create Catkin Workspace.
```
cd ~
mkdir -p ~/catkin_ws/src
cd ./catkin_ws/src
git clone https://github.com/catkin/catkin_simple
git clone https://github.com/ethz-asl/mav_comm.git
git clone git@github.com:uzh-rpg/agilicious_internal.git
```

```
cd ~/catkin_ws
catkin build
```
```

