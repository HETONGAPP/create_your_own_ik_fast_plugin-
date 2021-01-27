# create_your_own_ik_fast_plugin-
This repo will show you how to create your own ik plugin.

# Binary Install

sudo apt-get install ros-indigo-moveit-ikfast

# Source

Inside your catkin workspace

git clone https://github.com/ros-planning/moveit_ikfast.git

# Install openrave

sudo apt-get install ros-indigo-openrave

# Create Collada File For Use With OpenRave
rosrun collada_urdf urdf_to_collada <myrobot_name>.urdf <myrobot_name>.dae

rosrun collada_urdf urdf_to_collada new.urdf new.dae

rosrun moveit_ikfast round_collada_numbers.py <myrobot_name>.dae <myrobot_name>.rounded.dae 5

rosrun moveit_ikfast round_collada_numbers.py new.dae new.rounded.dae 5

# check the link index
openrave-robot.py <myrobot_name>.dae --info links

openrave-robot.py new.round.dae --info links

-----------------------------
world       0                
base_link   1     world      
arm_link_F2 2     base_link  
arm_link_F3 3     arm_link_F2
arm_link_F4 4     arm_link_F3
arm_link_F5 5     arm_link_F4
arm_link_F6 6     arm_link_F5
arm_link_F7 7     arm_link_F6
end         8     arm_link_F7
-----------------------------

# Generate IK Solver
before do this, you need to make some change for the ikfast.py which is located in /usr/local/lib/python2.7/dist-packages/openravepy/_openravepy_0_9

in the end of the file, exchange the two part

            if len(code) > 0:
                with open(options.savefile,'w') as f:
                    f.write(code)
        finally:
            openravepy.RaveDestroy()

# then run the code: python `openrave-config --python-dir`/openravepy/_openravepy_/ikfast.py --robot=<myrobot_name>.dae --iktype=transform6d --baselink=1 --eelink=8 --savefile=<ikfast_output_path>

python `openrave-config --python-dir`/openravepy/_openravepy_/ikfast.py --robot=new.round.dae --iktype=transform6d --baselink=1 --eelink=8 --savefile=/home/tong/catkin_ps/src/ikfast61.cpp

# Create Plugin

＃cd ~/catkin_ps/src
＃catkin_create_pkg <moveit_ik_plugin_pkg>

cd ~/catkin_ps/src
catkin_create_pkg ik_fast_plugin
cd ~/catkin_ps
catkin_make

# Create the plugin source code:

rosrun moveit_ikfast create_ikfast_moveit_plugin.py <myrobot_name> <planning_group_name> <moveit_ik_plugin_pkg> <ikfast_output_path>

# Or without ROS:

python /path/to/create_ikfast_moveit_plugin.py <myrobot_name> <planning_group_name> <moveit_ik_plugin_pkg> <ikfast_output_path>

Here we run the code

rosrun moveit_ikfast create_ikfast_moveit_plugin.py arm arm ik_fast_plugin /home/tong/catkin_ps/src/ikfast61.cpp
# arm is the group name which you have build in the moveit assistant

cd ~/catkin_ws
catkin_make

rosed arm_moveit_config/config/kinematics.yaml
change kinematics_solver: arm_arm_kinematics/IKFastKinematicsPlugin
kdl_kinematics_plugin/KDLKinematicsPlugin
# run the code
roslaunch arm_moveit_config demo_gazebo
