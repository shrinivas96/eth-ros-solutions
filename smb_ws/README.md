# Super Mega Bot (SMB) Simulation

The [first exersice](https://ethz.ch/content/dam/ethz/special-interest/mavt/robotics-n-intelligent-systems/rsl-dam/ROS2021/lec1/Exercise%20Session%201.pdf) is a basic set up to get aquanted with the basic commands and learning to write `.launch` files. Even if you have a [Destop-Full installation](http://wiki.ros.org/noetic/Installation/Ubuntu), like mine, then there are still a few things to install/prep before you are able to complete this exercise.

## Comipling the SMB Gazebo

1.  Download [smb_common](https://ethz.ch/content/dam/ethz/special-interest/mavt/robotics-n-intelligent-systems/rsl-dam/ROS2021/lec1/smb_common.zip) and place it in your git folder. Create a symlink by running:

        ln -s ~/path/to/git/folder/smb_common
    in the [smb_ws/src](smb_ws/src) folder 

2.  To comiple and run `smb_gazebo`, you'll need [`hector_gazebo`](https://github.com/tu-darmstadt-ros-pkg/hector_gazebo) and [`velodyne_simulator`](https://bitbucket.org/DataspeedInc/velodyne_simulator/src/master/). Download and place them in the git folder. 
    
    - Create a symlink for `hector_gazebo` in the `src` folder. 

    - Create a symlink for `velodyne_simulator` in the `/opt/ros/$ROS_DISTRO/share/` folder (for reasons that I don't undertsand yet). You'll also need sudo permisions for doing this.

3. Download `teleop_twist_keyboard` from [its GitHub page](https://github.com/ros-teleop/teleop_twist_keyboard). Place it in your git folder. In the lecture video the instructor says that we need to compile it from source (and not from your distribution). Create a symlink in your [smb_ws/src](smb_ws/src) folder. It will compile with all other packages when we build the entire workspace.

After following the above steps build the ` by running the following command in the [smb_ws](smb_ws) folder:
    
    catkin build smb_gazebo

and source your `devel/setup.bash` file. Now, running

    roslaunch smb_gazebo smb_gazebo.launch
opens the robot in a robocup environment. Play around wth the avalibale commands as they suggest in Point 2 of the sheet. 

Though you cannot `rosrun` the `teleop_twist_keyboard` yet. To do so, exit from the previous terminal session (Control-C) and run the following command:

    catkin build

(also in the [smb_ws](smb_ws) folder) and source `devel/setup.bash`. A closer look at the build messages (in comparision to before) reveals that this time `teleop_twist_keyboard` is also compiled:
```
[build] Found '9' packages in 0.0 seconds.                                     
[build] Updating package table.                                                
Starting  >>> hector_gazebo_plugins                                            
Starting  >>> hector_gazebo_thermal_camera                                     
Starting  >>> teleop_twist_keyboard                                            
Finished  <<< hector_gazebo_plugins                       [ 0.6 seconds ]      
Starting  >>> hector_gazebo_worlds                                             
Starting  >>> hector_sensors_gazebo                                            
Starting  >>> smb_description                                                  
Finished  <<< smb_description                             [ 0.3 seconds ]      
Starting  >>> smb_control                                                      
Finished  <<< smb_control                                 [ 0.3 seconds ]      
Starting  >>> smb_gazebo                                                       
Finished  <<< teleop_twist_keyboard                       [ 3.3 seconds ]      
Finished  <<< smb_gazebo                                  [ 1.6 seconds ]      
Finished  <<< hector_sensors_gazebo                       [ 3.0 seconds ]      
Finished  <<< hector_gazebo_worlds                        [ 3.1 seconds ]      
Finished  <<< hector_gazebo_thermal_camera                [ 28.0 seconds ]     
Starting  >>> hector_gazebo                                                    
Finished  <<< hector_gazebo                               [ 1.7 seconds ]      
[build] Summary: All 9 packages succeeded!                                     
[build]   Ignored:   None.                                                     
[build]   Warnings:  None.                                                     
[build]   Abandoned: None.                                                     
[build]   Failed:    None.                                                     
[build] Runtime: 29.9 seconds total.                                           
[build] Note: Workspace packages have changed, please re-source setup files to use them.
```

Launch `smb_gazebo` again with 
    
    roslaunch smb_gazebo smb_gazebo.launch

and in a different terminal session run the `teleop_twist_keyboard` with:

    rosrun teleop_twist_keyboard teleop_twist_keyboard.py

and use the keys to move the robot around. As explained in the video lecture, key press will only be recorded while in the `teleop_twist_keyboard` termial session. Use `rqt_graph` to inspect the several interconnected parts as a graph. Press Control-C to exit both the programs. Now, we will look at running both parts through a single `.launch` file.


## Using a `.launch` file to run both parts

The [smb_gazebo_robocup.launch](smb_ws/src/Ex1/smb_gazebo_robocup.launch) file is responsible to launch both the `smb_gazebo` simulation and `teleop_twist_keyboard` together. To run this file, run the following command, while in the [smb_ws](smb_ws) folder:

    roslaunch src/Ex1/smb_gazebo_robocup.launch

While still in the terminal use the keys to move the robot around. 

### Decoding the file

Inspecting the contents of the [smb_gazebo.launch](smb_ws/src/smb_common/smb_gazebo/launch/smb_gazebo.launch) file, it is clear that we need to pass the variable `world_file` while we include it in our launch file. Lines 2-5 define `world_file` as we would like to open the robocup environment. Then, in Lines 8-10, we include the `smb_gazebo` file with our defined variables. Finally, we open the `teleop_twist_keyboard` using the `<node />` tag. The main thing to note here is that in the `type` argument we need to pass the name of the executable that we want to run. 

