# OCS2 Mobile Manipulator

The `ocs2_mobile_manipulator` package supports various robotic arms and wheel-based mobile manipulators. The system model is determined by parsing the URDF and the task file. Currently, the following system models are supported:

* __Default__ (_value:_ 0): The default system model obtained by parsing the URDF
* __Actuated dummy wheel-base__ (_value:_ 1): Adds a dummy XY-Yaw joints to the the model parsed from the URDF which are actuated under holonomic constraint (velocity-control)
* __Unactuated Dummy floating-base__ (_value:_ 2): Adds a dummy XYZ-RPY joints to the the model parsed from the URDF which are unactuated
* __Actuated Dummy floating-base__ (_value:_ 3): Adds a dummy XYZ-RPY joints to the the model parsed from the URDF which are fully-actuated (velocity-control)

To play-around different model types, you can change the model-information in the `task.info` files.


## Examples - URDF

Over here, we specify the steps involved in creating the URDF used for the examples.


### Franka Panda

* In the `src` directory of your catkin workspace, clone the official repository of the [Franka Panda](https://www.franka.de/):

```bash
git clone git@github.com:frankaemika/franka_ros.git
```

* Build the necessary packages and source the workspace:

```bash
catkin build franka_description ocs2_robotic_assets

source devel/setup.bash
```

* Convert the xacro file to urdf format:

```bash
rosrun xacro xacro -o $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/franka/urdf/panda.urdf $(rospack find franka_description)/robots/panda_arm.urdf.xacro hand:=true
```

* Copy all meshes from `franka_description` to `ocs2_robotic_assets/resources` directory:

```bash
cp -r $(rospack find franka_description)/meshes $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/franka/meshes
```

* Replace the meshes locations in the robot's urdf

```bash
sed -i 's+franka_description+ocs2_robotic_assets/resources/mobile_manipulator/franka+g' $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/franka/urdf/panda.urdf
```

* Add a dummy link "root" to the URDF (KDL prefers the root of the tree to have an empty-link):

```xml
  ...
  <!-- Root link -->
  <link name="root"/>
  <joint name="root_joint" type="fixed">
    <origin rpy="0 0 0" xyz="0 0 0"/>
    <parent link="root"/>
    <child link="panda_link0"/>
  </joint>
  <!-- Robot Arm -->
  ...
```

### Kinova Jaco2

* In the `src` directory of your catkin workspace, clone the official repository of the [Kinova Jaco2](https://assistive.kinovarobotics.com/product/jaco-robotic-arm):

```bash
git clone git@github.com:Kinovarobotics/kinova-ros.git
```

* Build the necessary packages and source the workspace:

```bash
catkin build kinova_description ocs2_robotic_assets

source devel/setup.bash
```

* Convert the xacro file to urdf format:

```bash
# For Kinova Jaco2 (7-DOF)
rosrun xacro xacro -o $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/kinova/urdf/j2n7s300.urdf $(rospack find kinova_description)/urdf/j2n7s300_standalone.xacro
# For Kinova Jaco2 (6-DOF)
rosrun xacro xacro -o $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/kinova/urdf/j2n6s300.urdf $(rospack find kinova_description)/urdf/j2n6s300_standalone.xacro
```

* Copy all meshes from `franka_description` to `ocs2_robotic_assets/resources` directory:

```bash
cp -r $(rospack find kinova_description)/meshes $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/kinova/meshes
```

* Replace the meshes locations in the robot's urdf

```bash
# For Kinova Jaco2 (7-DOF)
sed -i 's+kinova_description+ocs2_robotic_assets/resources/mobile_manipulator/kinova+g' $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/kinova/urdf/j2n7s300.urdf
# For Kinova Jaco2 (6-DOF)
sed -i 's+kinova_description+ocs2_robotic_assets/resources/mobile_manipulator/kinova+g' $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/kinova/urdf/j2n6s300.urdf
```

* In addition to above, we make the following changes to the URDF Wto make it more readable:

    * Replace all arm joint types from "continuous" to "revolute" with high joint limits
    * (optional) Removed all `gazebo` tags, i.e.: `<gazebo>`, `<transmission>`
    * (optional) Removed the following links from the chain: `world`

### PR2

* In the `src` directory of your catkin workspace, clone the official repository of the [PR2 robot](https://robots.ieee.org/robots/pr2/):

```bash
git clone git@github.com:PR2/pr2_common.git
```

* Build the necessary packages and source the workspace:

```bash
catkin build pr2_description ocs2_robotic_assets

source devel/setup.bash
```

* Convert the xacro file to urdf format:

```bash
rosrun xacro xacro -o $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/pr2/urdf/pr2.urdf $(rospack find franka_description)/robots/pr2.urdf.xacro
```

* Copy all meshes from `pr2_description` to `ocs2_robotic_assets/resources` directory:

```bash
cp -r $(rospack find pr2_description)/meshes $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/pr2/meshes
cp -r $(rospack find pr2_description)/materials $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/pr2/materials
```

* Replace the meshes locations in the robot's urdf

```bash
sed -i 's+pr2_description+ocs2_robotic_assets/resources/mobile_manipulator/pr2+g' $(rospack find ocs2_robotic_assets)/resources/mobile_manipulator/pr2/urdf/pr2.urdf
```

* In addition to above, we make the following changes to the URDF Wto make it more readable:

    * Replace all arm joint types from "continuous" to "revolute" with high joint limits
    * Changed all "screw" joints (i.e. `l_gripper_motor_screw_joint`, `r_gripper_motor_screw_joint`, `torso_lift_motor_screw_link`) to fixed joints
    * (optional) Removed all `gazebo` tags, i.e.: `<gazebo>`, `<transmission>`