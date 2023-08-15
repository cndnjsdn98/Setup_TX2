# Setup_TX2
Step by Step instructions for setting up a Jetson TX2 for OCP packages utilizing Casadi and ACADOS

## Flashing Jetson TX2
To get to using a fresh Jetson TX2 we must first flash the board with a Jetson Linux. For this step you will need the [NVIDIA SDK Manager](https://developer.nvidia.com/sdk-manager).
1. If running Ubuntu 20.04 we need to trick the SDK Manager to think that we are on Ubuntu 18.04      by changing the VERSION_ID value with 18.04 on /etc/os-release.
  ```
  sudo vim /etc/os-release
  ```
   
