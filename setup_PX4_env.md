# Setup ROS and Catkin Workspace Environment for PX4 Autopilot and Agilicious
Step by Step instructions for setting up a ROS and Catkin_ws environment on Jetson TX2 for PX4 offboard flights.

## Install required Packages
Following instructions are for installing dependencies such as MAVROS and MAVLINK for communicating with the FCU.
```
sudo apt-get install ros-melodic-mavros ros-melodic-mavros-extras
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
chmod +x ./install_geographiclib_datasets.sh
sudo ./install_geographiclib_datasets.sh
rm ./install_geographiclib_datasets.sh
```


1. Required dependencies
```
sudo apt-get update
sudo apt-get install python3-vcstool \
                     ros-melodic-rqt-gui \
                     ros-melodic-rqt-gui-py \
                     ros-melodic-octomap-msgs \
                     ros-melodic-gazebo-plugins
```
1. Install required packages 
2. Create Catkin Workspace.
```
cd ~
mkdir -p ./catkin_ws/src
cd ./catkin_ws/src
git clone git@github.com:mavlink/mavros.git
git clone git@github.com:mavlink/mavlink.git --recursive

```
```
git clone git@github.com:PX4/PX4-Autopilot.git
cd PX4-Autopilot
git checkout stable
```
```
git clone git@github.com:uzh-rpg/rpg_quadrotor_control.git
git clone https://github.com/catkin/catkin_simple
git clone https://github.com/ethz-asl/mav_comm.git
git clone git@github.com:uzh-rpg/agilicious_internal.git
```

```
cd 
