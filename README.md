# Setup_TX2
Step by Step instructions for setting up a Jetson TX2 for OCP packages utilizing Casadi and ACADOS

## Flashing Jetson TX2
### Preparing OS Image
To get to using a fresh Jetson TX2 we must first flash the board with a Jetson Linux. For this step you will need to install [NVIDIA SDK Manager](https://developer.nvidia.com/sdk-manager).
1. If running Ubuntu 20.04 we need to trick the SDK Manager to think that we are on Ubuntu 18.04      by changing the VERSION_ID value with 18.04 on /etc/os-release.
  ```
  sudo vim /etc/os-release
  ```
2. Open NVIDIA SDK Manager.
3. In the "Hardware Configuration" unselect Host Machine and select the Target Hardware to be         Jetson TX2 modules
4. Choose JetPack 4.6.3 from the Target Operating System Drop-down
5. Once you have selected the appropriate target Hardware and Target Operating system, please hit     continue to proceed.
6. Ensure that "Jetson SDK Components" and "Download now. Install later" check boxes are              unchecked, and that "Jetson OS" and "I accept the terms and conditions of the license              agreements" are checked.
7. You may change your Download folder or the Target HW image folder if you wish.
8. Then proceed to the next step.
9. Downloading will begin and a prompt will pop up to flash your Jetson. On this prompt press skip.
10. Once the installation is complete close SDK Manager
11. Goto your Target HW image folder that was designated in step 7 of these instructions and find     the `Linux_For_Tegra` folder.
  ```
  cd ~/nvidia/nvidia_sdk/JetPack_4.6.3_Linux_JETSON_TX2_TARGETS/Linux_For_Tegra
  ```
12. From the [ConnectTech Board Support Page](https://connecttech.com/resource-center/l4t-board-support-packages/) download the driver for your breakout board of Jetpack 4.6.3.
  ```
  wget https://connecttech.com/ftp/Drivers/CTI-L4T-TX2-32.7.3-V001.tgz
  ```
13. Install the CTI-L4T BSP with the following commands
    ```
    tar -zxf CTI-L4T-TX2-32.7.3-V001.tgz
    cd CTI-L4T
    sudo ./install.sh
    cd .. # to return to the Linux_for_Tegra directory
    ```

### Flashing the Jetson
0. Ensure that the DIP Switch S1 is set to "ATX Mode" to ensure that the device does not start-up     automatically upon connecting the power. 
1. Connect your TX2 mounted on the breakout board to the computer via micro-USB and plug in the       power cable. 
2. Put your device in forced recovery mode by holding these buttons in the following order           (RECOVERY->POWER->RESET) and releasing them in the following order (POWER->RESET->RECOVERY).
3. Flash your Jetson by running the following commands from `Linux_for_Tegra directory`.
   ```
   sudo ./cti-flash.sh
   ```
4. Once the flashing has completed, the TX2 will reboot.

### Initial Setup Routine
After flashing the Jetson, perform initial setup routine by connecting it to a screen, mouse and a keyboard.

As a reference for the following steps, I named my board `Jetson-TX2-01` and my name on the OS to be `wonoo`

## Install Dependencies
If you would like to use your main PC to control your device connected via micro-USB, you can `ssh` into your device with the following commands. 
```
ssh wonoo@Jetson-TX2-01.local
```

### Instal ROS

### Install catkin

### Install ACADOS

### Install CasADi
The following steps are taken from instructions from [CasADi](https://github.com/casadi/casadi/wiki/InstallationLinux) and [Ipopt](https://coin-or.github.io/Ipopt/INSTALL.html#EXTERNALCODE) websites.

To get started grab dependencies:
```
sudo apt-get install gcc g++ gfortran git cmake liblapack-dev pkg-config --install-recommends
```
To complie the Python interface, you also need SWIG and a decent Python Installation:
```
sudo apt-get install swig ipython python-dev python-numpy python-scipy python-matplotlib --install-recommends
```

#### Install IPOPT
If you use the prebuilt CasADi binaries, IPOPT is included hwoever, here we will be building both CasADi and IPOPT from sources. More specifically we will be using [coinbrew](https://coin-or.github.io/coinbrew/) to automate the download of the source code which also deals with its dependencies: ASL, MUMPS and HSL. 
##### Download CoinHSL (recommended)
> To really benefit from from IPOPT, you should also try to get additional linear solvers, which can be done post-installation, regardless of how IPOPT was installed. We strongly recommend you to get at least the HSL solver MA27 (which is free). You will find instructions on that here. When filling out the forms for obtaining HSL, please mention that you plan to use the routines with Ipopt/CasADi. Unfortunately, we cannot guarantee the stability of using the newer linear solvers (MA57, MA77, MA86 and MA97 that are only free for academics) since HSL has not granted us any maintenance license.


0. I like to contain all the packages in a single folder `nlp_solvers`:
```
mkdir ~/nlp_solvers
cd ~/nlp_solvers
```
1. Download `coinbrew`:
```
wget https://raw.githubusercontent.com/coin-or/coinbrew/master/coinbrew
chmod u+x ./coinbrew
```
2. Fetch Ipopt project
```
./coinbrew fetch Ipopt --no-prompt
```




