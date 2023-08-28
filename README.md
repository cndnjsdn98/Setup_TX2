# Setup_TX2
Step by Step instructions for setting up a Jetson TX2 for OCP packages utilizing Casadi and ACADOS

# Flashing Jetson TX2
## Preparing OS Image
To get to using a fresh Jetson TX2 we must first flash the board with a Jetson Linux. For this step you will need to install [NVIDIA SDK Manager](https://developer.nvidia.com/sdk-manager).
1. If running Ubuntu 20.04 we need to trick the SDK Manager to think that we are on Ubuntu 18.04 by changing the `VERSION_ID` value with 18.04 on `/etc/os-release`.
  ```
  sudo vim /etc/os-release
  ```
2. Open NVIDIA SDK Manager.
3. In the "Hardware Configuration" unselect Host Machine and select the Target Hardware to be Jetson TX2 modules
4. Choose JetPack 4.6.3 from the Target Operating System Drop-down
5. Once you have selected the appropriate target Hardware and Target Operating system, please hit continue to proceed.
  ![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/63b7ac98-1d6e-4a8c-b87d-abf74734a71e)
6. Ensure that "Jetson SDK Components" and "Download now. Install later" check boxes are unchecked, and that "Jetson OS" and "I accept the terms and conditions of the license agreements" are checked.
7. You may change your Download folder or the Target HW image folder if you wish.
8. Then proceed to the next step.
![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/ff28b168-7cc7-4b1e-a835-def0f640f128)
9. Downloading will begin and a prompt will pop up to flash your Jetson. On this prompt press skip.
![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/8b47d5ad-f2b0-43ba-b048-f0f06c52fbd1)
10. Once the installation is complete close SDK Manager
![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/c363d269-ec4a-4598-911a-d57b6b9ea217)
11. Goto your Target HW image folder that was designated in step 7 of these instructions and find the `Linux_For_Tegra` folder.
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
14. Change `VERSION_ID` ON `/etc/os-release` back to 20.04 if using Ubuntu 20.04
```
sudo vim /etc/os-release
```
## Flashing the Jetson
0. Ensure that the DIP Switch S1 is set to "ATX Mode" to ensure that the device does not start-up automatically upon connecting the power. 
1. Connect your TX2 mounted on the breakout board to the computer via micro-USB and plug in the power cable. 
2. Put your device in forced recovery mode by holding these buttons in the following order (RECOVERY->POWER->RESET) and releasing them in the following order (POWER->RESET->RECOVERY).
3. Flash your Jetson by running the following commands from `Linux_for_Tegra directory`.
```
sudo ./cti-flash.sh
```
4. Once the flashing has completed, the TX2 will reboot.

### Initial Setup Routine
After flashing the Jetson, perform initial setup routine by connecting it to a screen, mouse and a keyboard.

As a reference for the following steps, I named my board `Jetson-TX2-01` and my name on the OS to be `wonoo`

# Installing Packages
If you would like to use your main PC to control your device connected via micro-USB, you can `ssh` into your device with the following commands. 
```
ssh wonoo@Jetson-TX2-01.local
```
## Install Cmake
Simply running `sudo apt-get install cmake` will not install the latest version of cmake here. The following instructions were taken from  [here](https://askubuntu.com/questions/355565/how-do-i-install-the-latest-version-of-cmake-from-the-command-line) to install the most recent version of cmake.

0. Prepare for installing cmake
```
sudo apt update && \
sudo apt install -y software-properties-common lsb-release && \
sudo apt clean all
```
1. Obtain a copy of kitware's signing key.
```
wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /etc/apt/trusted.gpg.d/kitware.gpg >/dev/null
```
2. Add kitware's repository to your sources list for Ubuntu Bionic Beaver (18.04).
```
sudo apt-add-repository "deb https://apt.kitware.com/ubuntu/ $(lsb_release -cs) main"
```
3. As an optional step, is recommended that we also install the kitware-archive-keyring package to ensure that Kitware's keyring stays up to date as they rotate their keys. 
```
sudo apt update
sudo apt install kitware-archive-keyring
sudo rm /etc/apt/trusted.gpg.d/kitware.gpg
```
If running `sudo apt update` gets the following error:
```
Err:7 https://apt.kitware.com/ubuntu bionic InRelease
The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6AF7F09730B3F0A4
Fetched 11.0 kB in 1s (7552 B/s)
```
Then copy the public key `6AF7F09730B3F0A4` and run this command:
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 6AF7F09730B3F0A4
```
4. Finally we can update and install `cmake`.
```
sudo apt update
sudo apt install cmake
```
## Setup Python Virtual Environment
1. Install pip for Python3
```
sudo apt update
sudo apt install python3-pip
```
2. Setup Python virtual environment for the packages. Here I've named my `virtualenv` to be `gp_mpc_venv` as the main purpose of my TX2 is for running GP_MPC & GP_MHE.
```
sudo pip3 install virtualenv
cd <PATH_TO_VENV_DIRECTORY>
virtualenv gp_mpc_venv --python=/usr/bin/python3.6
source gp_mpc_venv/bin/activate
```
## Install ROS
To install Robot Operatin System (ROS) Melodic on our device we use [installROS](https://github.com/jetsonhacks/installROS).
```
cd ~ # Go to the directory where you would like to check out installROS to
git clone https://github.com/jetsonhacks/installROS.git
cd installROS
./installROS.sh
```

## Install catkin
Install catkin_tools using apt-get with the following commands:
```
sudo sh \
  -c 'echo "deb http://packages.ros.org/ros/ubuntu `lsb_release -sc` main" \
    > /etc/apt/sources.list.d/ros-latest.list'
wget http://packages.ros.org/ros.key -O - | sudo apt-key add -
sudo apt-get update
sudo apt-get install python3-catkin-tools
```
## Install ACADOS
The following instructions were taken from the [Acados](https://docs.acados.org/installation/index.html) website to build and install `acados` with CMake.

1. Go to the desired directory and clone acados and its submodules by running
```
cd ~/
git clone https://github.com/acados/acados.git
cd acados
git submodule update --recursive --init
```
2. Install `acados` with the following commands:
```
mkdir -p build
cd build
cmake -DACADOS_WITH_QPOASES=ON -DACADOS_WITH_OSQP=ON ..
# add more optional arguments e.g. -DACADOS_WITH_OSQP=OFF/ON -DACADOS_INSTALL_DIR=<path_to_acados_installation_folder> above
make install -j4
```
4. Install Python Interface
```
cd ../
cd ./interfaces/acados_template
pip install -e ./
```
5. Add the following texts to `.bashrc` to add the path to the compiled shared libraries to `LD_LIBRARY_PATH`.
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:"<acados_root>/lib"
export ACADOS_SOURCE_DIR="<acados_root>"
```
6. To be able to successfully render C code templates download `t_render` binaries for Ubuntu from [here](https://github.com/acados/tera_renderer/releases/) and place them in `<acados_root>/bin` and strip the version and platform from the binary name.
```
cd ~/acados/bin
wget https://github.com/acados/tera_renderer/releases/download/v0.0.34/t_renderer-v0.0.34-linux
mv ./t_render-v0.0.34 ./t_render
chmod u+x ./t_render
```
7. Run a Python example to check that everything works:
```
cd ../examples/acados_python/getting_started
python ./minimal_example_ocp.py
```

## Install CasADi
The following steps are taken from instructions from [CasADi](https://github.com/casadi/casadi/wiki/InstallationLinux) and [Ipopt](https://coin-or.github.io/Ipopt/INSTALL.html#EXTERNALCODE) websites.

To get started grab dependencies:
```
sudo apt-get install gcc g++ gfortran git cmake liblapack-dev pkg-config --install-recommends
```
To complie the Python interface, you also need SWIG and a decent Python Installation:
```
sudo apt-get install swig ipython python-dev python-numpy python-scipy python-matplotlib --install-recommends
```
If CasADi was installed while installing `acados` python interface, uninstall that version as it will have dependency issues:
```
pip uninstall casadi
```

### Building IPOPT using coinbrew
If you use the prebuilt CasADi binaries, IPOPT is included hwoever, here we will be building both CasADi and IPOPT from sources. More specifically we will be using [coinbrew](https://coin-or.github.io/coinbrew/) to automate the download of the source code which also deals with its dependencies: ASL, MUMPS and HSL. 

> To really benefit from from IPOPT, you should also try to get additional linear solvers, which can be done post-installation, regardless of how IPOPT was installed. We strongly recommend you to get at least the HSL solver MA27 (which is free).

You can request a copy of these HSL solvers from [here](https://licences.stfc.ac.uk/product/coin-hsl)

>  When filling out the forms for obtaining HSL, please mention that you plan to use the routines with Ipopt/CasADi. Unfortunately, we cannot guarantee the stability of using the newer linear solvers (MA57, MA77, MA86 and MA97 that are only free for academics) since HSL has not granted us any maintenance license.

0. Install Dependencies.
```
sudo apt-get install libblas3 libblas-dev liblapack3 liblapack-dev gfortran
```
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
3. Inside the `ThirdParty/HSL` directory that `coinbrew` created unpack the Coin-HSL source code.
```
cd ./ThirdParty/HSL
gunzip coinhsl-x.y.z.tar.gz
tar xf coinhsl-x.y.z.tar
```
4. Rename or set a symbolic link of the `coinhsl-x.y.z` directory to `coinhsl`
```
ln -s coinhsl-x.y.z coinhsl
````
5. Finish buidling Ipopt. The script will automatically install the package so no need for the installation step. 
```
cd ../../
./coinbrew build Ipopt --prefix=/usr/local --test --no-prompt --verbosity=3
```
6. Create a symbolic link from `libcoinhsl.so` to `libhsl.so`:
```
cd /usr/local/lib
sudo ln -s ./libcoinhsl.so ./libhsl.so
```

### Building CasADi from sources
0. Go to directory you would like to check out the `CasADi` to, here I installed in `$HOME` directory and check out `CasADi` from GitHub.
```
cd ~
git clone https://github.com/casadi/casadi.git
```
1. Create a build directory for an out-of-source build:
```
cd casadi
mkdir build
cd build
```
2. Generate a makefile and build. The following options commands CasADi to be built using features and packages that we have installed
```
cmake -DWITH_PYTHON=ON -DWITH_IPOPT=ON -DWITH_LAPACK=ON  -DWITH_QPOASES=ON  ..
make
sudo make install
```
3. Run unittest to ensure that the installation was successful:
```
cd .. # Go back to the main casadi source directory
cd test/python
python alltests.py
```

## Install Other Jetson SDK Components
To install other Jetson SDK Components such as CUDA 10.2 come back to the main PC. 
0. Again, if you are not using Ubuntu 18.04, change the `VERSION_ID` value with 18.04 on `/etc/os-release`.
  ```
  sudo vim /etc/os-release
  ```
1. Open NVIDIA SDK Manager.
2. Again, deselect Host Machine, select Jetson TX2 as your Target HArdware, and the appropriate Jetpack version (which in this case is JEtpack 4.6.3) then hit continue.
![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/377198e4-2200-4e04-be1a-0d2c117a0dad)
3. From here, make sure to unselect Jetson OS and only select Jetson SDK Components. Then check "I accept the terms and conditions of the license agreements" checkbox and click Continue.
![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/2fd75074-2383-4fad-9f35-fb282bd149d0)
4. Downloading will begin and a prompt will pop up to install the SDK Components. On this prompt, type in your username and password to install the SDK Components on your Jetson TX2.
![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/d72933f3-f963-4458-84c9-6343628433f0)
6. Once the installation is complete, close SDK Manager.
7. If you changed the `VERSION_ID` value on `/etc/os-release/` then change it back to the correct value.
```
sudo vim /etc/os-release
```

## Install ZED SDK
The following instructions to install ZED SDK were derived from the [ZED](https://www.stereolabs.com/docs/installation/jetson/) website.
0. Install `zstd`
```
sudo apt install zstd
```
1. Goto the directory you would like to download ZED SDK files to. Here I will be downloading it in `~/zed`.
```
mkdir ~/zed
cd ~/zed
```
2. Download the ZED SDK for Jetpack from [here](https://www.stereolabs.com/developers/release/). Ensure that you select the correct version for the Jetpack version that is installed on your Jetson TX2.
```
wget https://download.stereolabs.com/zedsdk/4.0/l4t32.7/jetsons
```
3. Add execution permission to the installer using the chmod +x command.
```
chmod +x ./jetsons
```
4. Run the ZED SDK Installer.
```
./jetsons
```
5. At the beginning of the installation, the Software License will be displayed, hit q after reading it.
6. During the installation, you might have to answer some questions on the installation of dependencies, tools and samples. Type y for yes and n for no and hit Enter. Hit Enter to pick the default option.
7. Please note that installing the 'libv4l-dev' apt package at any point on jetson will break the hardware encoding/decoding support


