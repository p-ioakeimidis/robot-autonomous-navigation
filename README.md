# ROS1 Noetic Autonomous Navigation Stack (EKF + Path Planning)

This project implements a complete **autonomous navigation system** for a **differential drive mobile robot** using ROS1 Noetic. It extends a previously developed **EKF-based localization system** and adds global and local path planning capabilities using the ROS Navigation Stack.

  <img width="600" height="388" alt="path_following" src="https://github.com/user-attachments/assets/e245e38b-6359-4573-b099-3c2687b58b78" />

---

## Workspace Architecture

This project is designed to run inside a single ROS catkin workspace:

```text id="a1q8x7"
~/catkin_ws/src/
├── robot-ekf-ocalization/     # EKF localization system (existing repo)
├── robot-autonomous-navigation/    # THIS repository (navigation stack)
```

Both packages must be built together in the same workspace.

---

## System Overview

The system combines:

* Differential drive robot in Gazebo
* EKF-based sensor fusion localization (from separate repo)
* Static occupancy grid map
* ROS Navigation Stack (`move_base`)
* Global and local path planning

---

## Navigation Architecture

```text id="wq3k9p"
Sensor Data + Odometry
        ↓
   EKF Localization
        ↓
   map → odom → base_link TF
        ↓
        move_base
        ├── Global Planner (navfn)
        ├── Local Planner (DWA)
        ├── Global Costmap
        ├── Local Costmap
        ↓
       cmd_vel
        ↓
   Differential Drive Robot
```
<img width="780" height="319" alt="image" src="https://github.com/user-attachments/assets/52d312a4-b562-4db7-ae9c-b54c780dd377" />

Source: http://wiki.ros.org/move_base
---

## Robot Model

* Differential drive (two-wheel) robot
* URDF-based simulation model
* Gazebo-based environment
* Velocity-controlled (`cmd_vel`)
* Standard ROS TF tree:

  * `map`
  * `odom`
  * `base_link`

---

## Features

* Global path planning using **Navfn (Dijkstra-based)**
* Local obstacle avoidance using **DWA (Dynamic Window Approach)**
* Static map navigation using occupancy grid
* Rolling local costmap for obstacle detection
* Integration with external EKF localization system
* RViz-based interactive navigation goals

---

## Dependencies

Install required ROS packages:

```bash id="c8m3pd"
sudo apt update
sudo apt install ros-noetic-navigation \
                 ros-noetic-navfn \
                 ros-noetic-dwa-local-planner \
                 ros-noetic-costmap-2d \
                 ros-noetic-map-server
```

---

## Installation

Clone both repositories into the same catkin workspace:

```bash id="kq9x2a"
cd ~/catkin_ws/src

git clone https://github.com/p-ioakeimidis/robot-ekf-localization.git

git clone https://github.com/p-ioakeimidis/robot-autonomous-navigation.git
```

---

## Build workspace

```bash id="v3n7lf"
cd ~/catkin_ws
catkin_make
source devel/setup.bash
```

---

## Running the System

### 1. Start Gazebo + robot

```bash id="r9k2mz"
roslaunch first_robot sim.launch
```

### 2. Start EKF localization (from localization repo)

```bash id="m4q1bt"
roslaunch local_localization local_ekf.launch
roslaunch global_localization global_ekf.launch
```

### 3. Start navigation stack

```bash id="x7w9cp"
roslaunch move_base_nav move_base_nav.launch
```

---

## Navigation Workflow (RViz)

1. Launch RViz
2. Set initial pose (if needed)
3. Click **“2D Nav Goal”**
4. Select target position

The robot will:

* Compute a global path (Navfn)
* Avoid obstacles (DWA planner)
* Continuously replan if needed
* Reach the target autonomously

---

## Key ROS Topics

* `/cmd_vel` → velocity commands
* `/odom` → raw odometry
* `/odometry/filtered` → EKF output (from localization repo)
* `/map` → occupancy grid
* `/move_base/goal` → navigation goal
* `/move_base/NavfnROS/plan` → global path
* `/move_base/DWAPlannerROS/local_plan` → local trajectory

---

## Planner Configuration

### Global Planner

* Algorithm: Navfn (Dijkstra-based)
* Frame: `map`

### Local Planner

* Algorithm: DWA
* Frame: `odom`

### Costmaps

* Global: static map-based
* Local: rolling window with obstacle detection

---

## Future Improvements

* TEB local planner integration for smoother trajectories
* Dynamic obstacle avoidance
* SLAM-based mapping instead of static maps
* Multi-goal waypoint navigation system
* Migration to ROS2 Navigation Stack (Nav2)

---

## Demo

(Add GIF or screenshots of RViz navigation here)

---

## Author

Developed by **Panagiotis Ioakeimidis**
Focus: Robotics, SLAM, State Estimation, Autonomous Navigation
