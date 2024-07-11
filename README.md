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
### Option A:  Using APT Repositories (Note: hasn't worked as of January 2024 for me)
0. Prepare for installing cmake
```
sudo apt update && \
sudo apt install -y software-properties-common lsb-release && \
sudo apt clean all \
sudo apt remove --purge --auto-remove cmake
```
1. Obtain a copy of kitware's signing key.
```
wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null
```
2. Add kitware's repository to your sources list for Ubuntu Bionic Beaver (18.04).
```
sudo apt-add-repository "deb https://apt.kitware.com/ubuntu/ $(lsb_release -cs) main"
```
3. As an optional step, is recommended that we also install the kitware-archive-keyring package to ensure that Kitware's keyring stays up to date as they rotate their keys. 
```
sudo apt update && \
sudo apt install kitware-archive-keyring && \
sudo rm /etc/apt/trusted.gpg.d/kitware.gpg
```
If running `sudo apt update` gets the following error:
```
Err:7 https://apt.kitware.com/ubuntu bionic InRelease
The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6AF7F09730B3F0A4
Fetched 11.0 kB in 1s (7552 B/s)
```
Then copy the public key from the errror message above (which in my case was `6AF7F09730B3F0A4`) and run this command:
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 6AF7F09730B3F0A4
```
4. Finally we can update and install `cmake`.
```
sudo apt update
sudo apt install -y cmake
```

### Option B: Building and Installing from Source (Recommended for developers)
0. Install GCC tools:
```
sudo apt update
sudo apt install -y build-essential libtool autoconf unzip wget libssl-dev
```
1. Uninstall default version of cmake
```
sudo apt remove --purge --auto-remove cmake
```
2. Goto [official CMake webpage](https://cmake.org/download) and copy the download link to the the source distribution of the latest version (or the specific version you'd like) and download and unzip in your TX2 using `wget`:

```
wget https://github.com/Kitware/CMake/releases/download/v3.27.9/cmake-3.27.9.tar.gz
tar -xzvf cmake-3.27.9.tar.gz
```
3. Install the extracted source by running:
```
cd cmake-3.27.9/
./bootstrap
make -j$(nproc)
sudo make install
```

4. Test your new cmake version:
```
cmake --version
```
## Install Python 3.8.10
1. Update the packages list and install the packages necessary to build Python:
```
sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev libsqlite3-dev wget libbz2-dev
```

2. Download the desired version of the source code from the Python download page. Here I will be installing Python 3.8.10:
```
wget https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tgz
```

3. Extract the gzipped archive:
```
tar -xzf Python-3.8.10.tgz
```

4. Switch to the Python source directory and execute the `configure` script to checks that all of the dependencies are met:
```
cd Python-3.8.10
./configure --enable-optimizations
```

5. Start build process:
```
make -j 8
```

6. Install the Python binaries. Do not use `make install` as it will overwrite the default system python3 binary and could cause issues with your system.
```
sudo make altinstall
```

7. Verify that Python 3.8 has been installed correctly by typing:
```
python3.8 --version
```
The output should show the correct Python Version:
```
Python 3.8.10
```
   
## Setup Python Virtual Environment
1. Install pip for Python3
```
sudo apt update
sudo apt install -y python3-pip
python3.8 -m pip install --upgrade pip
```
2. Install matplotlib first as it always throws error when installing in virtual environment
```
sudo apt install python3-matplotlib
```

3. Setup Python virtual environment for the packages. 
```
python3.8 -m pip install virtualenv

cd <PATH_TO_VENV_DIRECTORY>
virtualenv env --python=/usr/local/bin/python3.8
source <PATH_TO_VENV_DIRECTORY>/env/bin/activate
```
## Install ROS
To install Robot Operatin System (ROS) Melodic on our device we use [installROS](https://github.com/jetsonhacks/installROS).
```
cd ~ # Go to the directory where you would like to check out installROS to
git clone https://github.com/jetsonhacks/installROS.git
cd installROS
./installROS.sh
```
To be ensure that ROS can communicate between your master Node (my main laptop for my case) edit your `ROS_MASTER_URI` and `ROS_IP` in `~/.bashrc` file. In my case my laptop name, where my Master ROSCORE, is `wonoo-Precision-5570` and the IP of my TX2 is `10.204.228.191` which can be found by running `hostname -I`:
```
export ROS_MASTER_URI=http://wonoo-Precision-5570.local:11311
export ROS_IP=10.204.228.191
```

## Install catkin
Install catkin_tools using apt-get with the following commands:
```
sudo sh \
  -c 'echo "deb http://packages.ros.org/ros/ubuntu `lsb_release -sc` main" \
    > /etc/apt/sources.list.d/ros-latest.list'
wget http://packages.ros.org/ros.key -O - | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y python3-catkin-tools
```
## Install ACADOS
The following instructions were taken from the [Acados](https://docs.acados.org/installation/index.html) website to build and install `acados` with CMake.

1. Go to the desired directory and clone acados and its submodules by running
```
cd ~/
git clone --depth 1 --branch v0.3.0 https://github.com/acados/acados.git
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
Go in to the directory of the `acados_template` to install python interface.
```
cd ../
cd ./interfaces/acados_template
```
Sepcify the Numpy version to be `1.19.4` to ensure it does not run in to issues while proceeding with the rest of the installations. In the `setup.py` file change the line 56 from `'numpy',` to `'numpy==1.19.4',`.
```
vim ./setup.py
```
Downgrade the setuptools_scm package to a version that is compatible with Python 3.6. 
```
pip install 'setuptools_scm<8'
```
Install the Python interface, the option `-e` sets the installation file to be edittable later on such that you can upgrade the installation if there is updates to the `acados` pakcage.
```
pip install -e ./
```
5. Add the following texts to `.bashrc` to add the path to the compiled shared libraries to `LD_LIBRARY_PATH`.
```
echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:"$HOME/acados/lib"' >> ~/.bashrc
echo 'export ACADOS_SOURCE_DIR="$HOME/acados"' >> ~/.bashrc
```
6. To be able to successfully render C code templates download `t_renderer` binaries for Ubuntu from [here](https://github.com/acados/tera_renderer) and place them in `<acados_root>/bin`. However, sometimes this can cause issues. To build `t_renderer` binary yourself, follow the following instructions 7 to 9, else skip to 10 after downloading the `t_renderer` binaries. 
7. install `cargo`
```
sudo apt-get -y install cargo
```
8. Build tera renderer.
```
cd ~/acados/interfaces/acados_template/tera_renderer
cargo build --verbose --release
```
If the above throws an error regarding incorrect version of `rust` then execute the following to update the rustc version. If there were no errors proceed to the next step:

Uninstall the initial `rustc` installation and install `rustup`
```
sudo apt autoremove rustc -y
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
Add `~/.cargo/bin` to the `$PATH` by adding the following line to `.bashrc`
```
echo 'export PATH=~/.cargo/bin:$PATH' >> ~/.bashrc 
```
Source `.bashrc` to source `cargo`
```
source ~/.bashrc
```
Install the needed version of `rust`
```
rustup default <required version of rust>
```
Build tera renderer
```
cargo build --verbose --release
```

9. Copy the built `t_renderer` binary to `acados` directory
```
cp ~/acados/interfaces/acados_template/tera_renderer/target/release/t_renderer ~/acados/bin/
```

10. <del> Run a Python example to check that everything works: </del>
```
cd ~/acados/examples/acados_python/getting_started
python ./minimal_example_ocp.py
```
<del> Note that this may return `ModuleNotFoundError: No module named '_casadi'` as we have yet to install `CasADi` properly yet. </del>

**As of July 2024 this has been returning the following error: `Segmentation fault (core dumped)`. This error will be resolved once `CasADi` is installed properly as instructed in the next step.**


## Install CasADi
The following steps are taken from instructions from [CasADi](https://github.com/casadi/casadi/wiki/InstallationLinux) and [Ipopt](https://coin-or.github.io/Ipopt/INSTALL.html#EXTERNALCODE) websites.

To get started grab dependencies:
```
sudo apt-get install -y gcc g++ gfortran git cmake liblapack-dev pkg-config --install-recommends
```
To complie the Python interface, you also need SWIG and a decent Python Installation:
```
sudo apt-get install -y swig ipython python-dev python-numpy python-scipy python-matplotlib --install-recommends
```
If CasADi was installed while installing `acados` python interface, uninstall that version as it will have dependency issues:
```
pip uninstall -y casadi
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
3. Run unittest to ensure that the installation was successful (Note: This can be very long):
```
cd .. # Go back to the main casadi source directory
cd test/python
python alltests.py
```
Alternative and less intensive test to ensure the installation was successful is by running Step 10 from [Install Acados](#Install-ACADOS) to run a minimal example provided by Acados:
```
cd ~/acados/examples/acados_python/getting_started
python ./minimal_example_ocp.py
```

## Gain access to the serial console device
To have permission to utilize the UART devices on your carrier board, add your Linux user to the `dialout` group by running the following:
```
sudo usermod -a -G dialout $USER
sudo usermod -a -G tty $USER
sudo chown $USER: /dev/ttyS0
```

Reboot the device for the change to take place.


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
![image](https://github.com/cndnjsdn98/Setup_TX2/assets/50539326/f3a2c02b-9dc5-4705-aaf2-557d5be1b164)
8. If you changed the `VERSION_ID` value on `/etc/os-release/` then change it back to the correct value.
```
sudo vim /etc/os-release
```

## Initialize your GitHub SSH Key
0. Firstly generate new SSH Key to add to your GitHub account.
Generate your SSH key by copying the following text in to the terminal and follwing the prompts, replacing the email used in the example with your GitHub email address. For more detailed instructions see [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
Copy the contents of `id_ed25519.pub` file displayed in the terminal to your clipboard with the following command.
```
cat ~/.ssh/id_ed25519.pub
```
Paste your public key (the copied content) in to your account. For more detailed instructions see [here](https://docs.github.com/en/enterprise-cloud@latest/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)


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


