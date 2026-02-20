<p align="center">TurtleBot3 SLAM Project</p>  
## Install Dependents
### ROS2
$ sudo apt install ros-humble-gazebo-*  
$ sudo apt install ros-humble-cartographer  
$ sudo apt install ros-humble-cartographer-ros  
$ sudo apt install ros-humble-navigation2  
$ sudo apt install ros-humble-nav2-bringup  
### TurtleBot3 Packages
$ source /opt/ros/humble/setup.bash  
$ mkdir -p ~/turtlebot3_ws/src  
$ cd ~/turtlebot3_ws/src/  
$ git clone -b humble https://github.com/ROBOTIS-GIT/DynamixelSDK.git  
$ git clone -b humble https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git  
$ git clone -b humble https://github.com/ROBOTIS-GIT/turtlebot3.git  
$ sudo apt install python3-colcon-common-extensions  
$ cd ~/turtlebot3_ws  
$ colcon build --symlink-install  
$ echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc  
$ source ~/.bashrc  
### Environment Configuration
$ echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc  
$ echo 'source /usr/share/gazebo/setup.sh' >> ~/.bashrc  
$ echo 'source /opt/ros/humble/setup.bash' >> ~/.bashrc
$ source ~/.bashrc  
**These commands were executed on the remote PC.**  
## Configure the Raspberry Pi
**After installing Ubuntu 22.04 for Raspberry Pi, on TurtleBot3 SBC:**   
**Network configuration:**  
$ sudo nano /etc/netplan/50-cloud-init.yaml  
**Edit the automatic update settings:**  
$ sudo nano /etc/apt/apt.conf.d/20auto-upgrades  
**Change the update settingsa as below:**  
APT::Periodic::Update-Package-Lists "0";  
APT::Periodic::Unattended-Upgrade "0";  
**Set mask for the process:**  
$ systemctl mask systemd-networkd-wait-online.service  
**Disable Suspend and Hibernation:**  
$ sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target  
**Reboot the Raspberry Pi:**  
$ ssh ubuntu@{IP Address of Raspberry PI}(for our own projet, we use $ ssh turtlebot@192.168.10.185)  
## Install packages on Raspberry PI
$ sudo apt install python3-argcomplete python3-colcon-common-extensions libboost-system-dev build-essential  
$ sudo apt install ros-humble-hls-lfcd-lds-driver  
$ sudo apt install ros-humble-turtlebot3-msgs  
$ sudo apt install ros-humble-dynamixel-sdk  
$ sudo apt install ros-humble-xacro  
$ sudo apt install libudev-dev  
$ mkdir -p ~/turtlebot3_ws/src && cd ~/turtlebot3_ws/src  
$ git clone -b humble https://github.com/ROBOTIS-GIT/turtlebot3.git  
$ git clone -b humble https://github.com/ROBOTIS-GIT/ld08_driver.git  
$ git clone -b humble https://github.com/ROBOTIS-GIT/coin_d4_driver  
$ cd ~/turtlebot3_ws/src/turtlebot3  
$ rm -r turtlebot3_cartographer turtlebot3_navigation2  
$ cd ~/turtlebot3_ws/  
$ echo 'source /opt/ros/humble/setup.bash' >> ~/.bashrc  
$ source ~/.bashrc  
$ colcon build --symlink-install --parallel-workers 1  
$ echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc  
$ source ~/.bashrc  
**USB Port Settings for OpenCR:**  
$ sudo cp `ros2 pkg prefix turtlebot3_bringup`/share/turtlebot3_bringup/script/99-turtlebot3-cdc.rules /etc/udev/rules.d/  
$ sudo udevadm control --reload-rules  
$ sudo udevadm trigger  
**ROS Domain ID Setting:**  
$ echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc(For our own projet, we use ID=26)  
$ source ~/.bashrc  
$ echo 'export LDS_MODEL=LDS-01' >> ~/.bashrc  
$ source ~/.bashrc  
## OpenCR Setup
**On turtlebot3 SBC:**  
$ sudo dpkg --add-architecture armhf  
$ sudo apt-get update  
$ sudo apt-get install libc6:armhf  
$ export OPENCR_PORT=/dev/ttyACM0  
$ export OPENCR_MODEL=burger  
$ rm -rf ./opencr_update.tar.bz2  
$ wget https://github.com/ROBOTIS-GIT/OpenCR-Binaries/raw/master/turtlebot3/ROS2/latest/opencr_update.tar.bz2  
$ tar -xvf opencr_update.tar.bz2  
$ cd ./opencr_update  
$ ./update.sh $OPENCR_PORT $OPENCR_MODEL.opencr  
## Bringup
**On remote PC, to connect to the Raspberry Pi:**  
$ ssh ubuntu@{IP_ADDRESS_OF_RASPBERRY_PI}(For our own projet, we use $ ssh turtlebot@192.168.10.185)  
**On TurtleBot3 SBC:**  
$ export TURTLEBOT3_MODEL=burger  
$ ros2 launch turtlebot3_bringup robot.launch.py  
**Both on remote PC and turtlebot3 SBC:**  
$ export ROS_DOMAIN_ID=30  
$ export RMW_IMPLEMENTATION=rmw_fastrtps_cpp  
## Slam
**On turtlebot3 SBC:**  
$ export TURTLEBOT3_MODEL=burger  
$ ros2 launch turtlebot3_bringup robot.launch.py  
**On remote PC:**  
$ export TURTLEBOT3_MODEL=burger  
$ ros2 launch turtlebot3_cartographer cartographer.launch.py  
**On remote PC, in a new terminal:**  
$ export TURTLEBOT3_MODEL=burger  
$ ros2 run turtlebot3_teleop teleop_keyboard  
**Use the keyboard to control the TurtleBot to move within the pre-mapped real environment, maintain a relatively stable speed, and traverse the entire map until the boundaries and obstacles are clearly displayed on the PC.**  
**To save the map:**  
$ ros2 run nav2_map_server map_saver_cli -f ~/mymap  
## Navigation
**On remote PC:**  
$ ssh ubuntu@{IP_ADDRESS_OF_RASPBERRY_PI}  
$ export TURTLEBOT3_MODEL=${TB3_MODEL}  
$ ros2 launch turtlebot3_bringup robot.launch.py  
**In a new terminal on remote PC:**  
$ export TURTLEBOT3_MODEL=burger  
$ ros2 launch turtlebot3_navigation2 navigation2.launch.py map:=$HOME/mymap.yaml  
**Before the navigation, we need to estimate initial pose, and use keyboard to precisely locate the robot on the map.**  
**After estimation 2D, we could set navigation goal to make the TurtleBot start moving to the destination immediately.**
