# Nomenclature 
- The computer that the mocap system is running on will be called `MocapComp`.
- The onboard computer on the drone will be called `DroneComp`
- the offboard computer will be called `OffboardComp`

# Set-up (physical)
## The flight controller
we use the [pixhawk Cube Blue](https://irlock.com/products/pixhawk2-1-blue-cube?variant=16025285230643). This cube is D.o.D. compliant. An alternative is the [Cube Orange](https://irlock.com/collections/cubepilot/products/cube-orange-standard-set-imu-v8) which is similar in quality and cheaper but not D.o.D. compliant.

## The Frame
We use a frame kit for the drone. spesifically we have used the [S500 frame](https://www.amazon.com/Readytosky-Quadcopter-Stretch-Version-Landing/dp/B01N0AX1MZ)

## The motors
We are using [Tmotors](https://store.tmotor.com/goods-334-MN3110.html)
## The ESCs
We are using [30A ESCs](https://www.amazon.com/QWinOut-Brushless-Controller-Multicopter-Quadcopter/dp/B07SFLJJQ5?th=1)
## The Props
We are using [11x4.7SFP Props](https://www.apcprop.com/product/11x4-7sfp/)
 
## Cable Config
Here is a link to the [pin layout for the Cube Blue mini board](https://docs.cubepilot.org/user-guides/carrier-boards/mini-carrier-board).

#### TELEM1
Set this up to one end of a radio temelemetry. connet the paired other end of the telemetry to your OffboardComp. 
#### TELEM2
you need a cable that goes from TELEM2 to usb on the OffboardComp. This cable is an FTDI Cable. This cable is nescisary for getting any ROS and MAVROS comunications working. You will probably need to make this cable. I would start by buying an [FTDI cable](https://www.amazon.com/Ximimark-FT232RL-Serial-Adapter-Arduino/dp/B07T8YHBH1) **CUT THE POWER CABLE (RED) BEFORE PLUGGING IT IN**

# Set-up (Code)
On the DroneComp and the OffboardComp install the following. This installs ros and all the dependencies that will be needed for runing future code (you can skip thsi step if your system is already sert up).\
https://github.com/uf-reef-avl/reef_auto_install
- Clone Repository
- cd into Repository
- Run ``./autoinstall.sh``
- Follow prompts
	
https://ardupilot.org/dev/docs/ros-install.html
- ``sudo apt-get install ros-noetic-mavros ros-noetic-mavros-extras``
- ``wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh``
- ``chmod a+x install_geographiclib_datasets.sh``
- ``sudo ./install_geographiclib_datasets.sh``
- ``sudo apt-get install ros-noetic-rqt ros-noetic-rqt-common-plugins ros-noetic-rqt-robot-plugins``

https://docs.px4.io/main/en/dev_setup/dev_env_linux_ubuntu.html
- ``git clone https://github.com/PX4/PX4-Autopilot.git --recursive``
- ``bash ./PX4-Autopilot/Tools/setup/ubuntu.sh``
	
**Restart computer**

If your DronComp doesnt already have a catkin workspace make one.\
https://industrial-training-master.readthedocs.io/en/melodic/_source/session1/Create-Catkin-Workspace.html
- ``cd ~/``
- ``mkdir --parents catkin_ws/src``
- ``cd catkin_ws``
- ``catkin init``
- ``catkin build``
- ``gedit ~/.bashrc``
- ``source ~/catkin_ws/devel/setup.bash``
	
**try this when things wont build first**
* ``catkin clean``
* ``ls``
* ``catkin build``
* ``ls``

# QGroundControl
Set up QGroundControl on the OffboardComp.

## Download
 https://docs.qgroundcontrol.com/master/en/getting_started/download_and_install.html
 - Download QGroundControl
 - ``chmod +x ./QGroundControl.AppImage``
 - ``./QGroundControl.AppImage``\
**To make launching QGround control easier I recommend you creat an alias in .bashrc**
- ``cd``
- ``nano .bashrc``
- add in something like ``alias QGC=./QGroundControl.AppImage``

## Flashing with px4
- plug your pixhawk into the your OffboardComp via micro USB to USB
- launch QGC
- wait for it to say connected (may take a while)
- click on the Q in the upper left corner
- Vehicle Setup
- Firmware
- 

## Calibrate (Pixhawk)
- 

## Calibrate (Telemetry)
- click on the Q in the upper left corner
- Vehicle Setup
- Radio
- Calibrate
- Follow prompts

## Uploading Parameters
To get the proper comunication channels up and running we need to set perameters in QGC.\
[v1.13](https://github.com/PX4/PX4-user_guide/blob/v1.13/en/ros/external_position_estimation.md) \
[v1.14](https://github.com/PX4/PX4-user_guide/blob/v1.14/en/ros/external_position_estimation.md) \
- click on the Q in the upper left corner
- Vehicle Setup
- Perameters\
**Method 1(recomended):**
- download [parameters](https://github.com/Alopez6991/2023_Drone_AVL_UNR/blob/main/params/REEF_PARAMS_06_23.params)
- tools (top right)
- upload parameters\
**Method 2 (v1.13) (hand set the parameters yourself):**\
After setting each parameter reatart the drone by going to parameters, tools, restart vehicale
- MAV_1_CONFIG: 102 (Telem 2)
- MAV_USEHILGPS: 1 (Enabled)
- EKF2_HGT_MODE: 3 (Vision)
- EKF2_AID_MASK: 24 (vision position fusion, vision yaw fusion)
**Method 2 (v1.14) (hand set the parameters yourself):**\
After setting each parameter reatart the drone by going to parameters, tools, restart vehicale
- MAV_1_CONFIG: 102 (Telem 2)
- EKF2_EV_CTRL: 15 (Horizontal position, vertical position, 3D velocity, yaw)
- EKF2_HGT_REF: Vision



# Mocap to DroneComp
go to the below repo and clone it into your catkin workspace.on the DroneComp\
https://github.com/Alopez6991/ros_vrpn_client
- ``cd``
- ``cd catkin_ws/``
- ``cd src/``
- ``git clone https://github.com/Alopez6991/ros_vrpn_client.git``
- ``cd ../``
- ``catkin build``
- ``roscd ros_vrpn_client``
- ``cd launch``
- ``nano test.launch``
- edit ``<arg name="name" default="magCheck" />`` (line 2) with the name of your object in mocap. e.g. ``<arg name="name" default="flybot" />``

## setting up telemetry
You should have telemetry plugged into telem1 on your pixhawk board. Find its matching transponder and plug it into your OffboardComp. On the OffboardComp run the following.\ 
https://arduino.stackexchange.com/questions/74714/arduino-dev-ttyusb0-permission-denied-even-when-user-added-to-group-dialout-o
- ``sudo usermod -a -G dialout your-username``
- ``sudo apt purge modemmanager``
# Mocap to Mavros
Clone the mocap to mavros package now. 
- ``cd``
- ``cd catkin_ws/``
- ``cd src/``
- ``git clone https://github.com/Alopez6991/vrpn_mavros.git``
- ``cd ../``
- ``catkin build``
- ``roscd vrpn_mavros``
- ``cd launch``
- ``nano test.launch``
- edit ``<arg name="name" default="platypus" />`` (line 2) with the name of your object in mocap. e.g. ``<arg name="name" default="flybot" />``
- edit ``<arg name="fcu_url" default="/dev/ttyUSB0:921600" />`` (line 3) with the dev path to your telem2 cable.
	- **Note that you need to have the pixhawk connected to the DroneComp via [telem2 and USB](#cable-config) otherwise it wont use MAVROS and MAVLINK**

# Tie it all Together
- Toss enough mocap dots on your system such that there is no symetry
- Creat a ridged body in your mocap softwear and give it a unique name
- Update the launch files to reflect the name of your mocap object
- Place the drone in the flight space
- On the DroneComp run: ``roscore``
- On the DroneComp run: ``roslaunch vrpn_mavros test.launch``
- ``rostopic echo /mavros/local_position/pose``
	- check that there is data being published to this topic
- Move the drone around and make sure the values you see make sense
- When you are ready to do your [First Flight](https://github.com/Alopez6991/2023_Drone_AVL_UNR/blob/main/First_Flight_Instructions.md) pop over there.

