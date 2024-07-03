# Setup ROS and Catkin Workspace Environment for PX4 Autopilot and Agilicious
Step by Step instructions for setting up a ROS and Catkin_ws environment on Jetson TX2 for PX4 offboard flights.

## Install required Packages
Following instructions are for installing dependencies such as MAVROS and MAVLINK for communicating with the FCU.

1. Required dependencies
```
sudo apt-get install ros-melodic-mavros ros-melodic-mavros-extras -y
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
chmod +x ./install_geographiclib_datasets.sh
sudo ./install_geographiclib_datasets.sh
rm ./install_geographiclib_datasets.sh
```

2. Install required packages 
```
sudo apt-get update
sudo apt-get install -y libgoogle-glog-dev \
                        protobuf-compiler \
                        ros-$ROS_DISTRO-octomap-msgs \
                        ros-$ROS_DISTRO-octomap-ros \
                        ros-$ROS_DISTRO-joy \
                        ros-${ROS_DISTRO}-mavros-msgs \
                        ros-${ROS_DISTRO}-rqt-gui \
                        ros-${ROS_DISTRO}-rqt-gui-py
```

3. First-Time Git Setup & Generating SSH Key
Setting up your identity:
```
git config --global user.name "<Your Name>"
git config --global user.email <your_email@example.com>
```
Generating a new SSH key
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
Copy the SSH key to your clipboard.
```
cat ~/.ssh/id_ed25519.pub
# Then select and copy the contents of the id_ed25519.pub file
# displayed in the terminal to your clipboard
```

4. Create Catkin Workspace.

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

