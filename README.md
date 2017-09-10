[![Udacity - Robotics NanoDegree Program](https://s3-us-west-1.amazonaws.com/udacity-robotics/Extra+Images/RoboND_flag.png)](https://www.udacity.com/robotics)
# 3D Perception 3D感知
Before starting any work on this project, please complete all steps for [Exercise 1, 2 and 3](https://github.com/udacity/RoboND-Perception-Exercises). At the end of Exercise-3 you have a pipeline that can identify points that belong to a specific object.

在开始此项目的任何工作之前，请完成[练习1，2和3](https://github.com/udacity/RoboND-Perception-Exercises) 的所有步骤。 在练习3结束时，您将有一个可以识别属于特定对象的点的管道。

In this project, you must assimilate your work from previous exercises to successfully complete a tabletop pick and place operation using PR2.

在这个项目中，您必须从以前的练习中吸收您的工作成功完成使用PR2的桌面拾取和放置操作。

The PR2 has been outfitted with an RGB-D sensor much like the one you used in previous exercises. This sensor however is a bit noisy, much like real sensors.

PR2已经配备了一个RGB-D传感器，就像以前的练习中使用的一样。 然而，这个传感器有点嘈杂，很像真实的传感器。

Given the cluttered tabletop scenario, you must implement a perception pipeline using your work from Exercises 1,2 and 3 to identify target objects from a so-called “Pick-List” in that particular order, pick up those objects and place them in corresponding dropboxes.

考虑到混乱的桌面情景，您必须使用练习1,2和3中的工作来实现感知流水线，以特定顺序从所谓的“选择列表”中识别目标对象，拿起这些对象并将其放在相应的对象中收存箱。

# Project Setup 项目设置
For this setup, catkin_ws is the name of active ROS Workspace, if your workspace name is different, change the commands accordingly
If you do not have an active ROS workspace, you can create one by:

对于此设置，catkin_ws是活动的ROS Workspace的名称，如果您的工作区名称不同，请相应更改命令
如果您没有活动的ROS工作区，则可以通过以下方式创建一个：

```sh
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/
$ catkin_make
```

Now that you have a workspace, clone or download this repo into the src directory of your workspace:

现在您有一个工作区，克隆或下载到您的工作区的src目录中：
```sh
$ cd ~/catkin_ws/src
$ git clone https://github.com/udacity/RoboND-Perception-Project.git
```

### Note: If you have the Kinematics Pick and Place project in the same ROS Workspace as this project, please remove the 'gazebo_grasp_plugin' directory from the `RoboND-Perception-Project/` directory otherwise ignore this note. 

### 注意：如果您将Kinematics Pick and Place项目放在与本项目相同的ROS Workspace中，请从 `RoboND-Perception-Project/` 目录中删除 'gazebo_grasp_plugin' 目录，否则忽略此注释。

Now install missing dependencies using rosdep install:

现在使用rosdep install安装缺少的依赖项：
```sh
$ cd ~/catkin_ws
$ rosdep install --from-paths src --ignore-src --rosdistro=kinetic -y
```
Build the project:

建设项目：
```sh
$ cd ~/catkin_ws
$ catkin_make
```
Add following to your .bashrc file

添加以下.bashrc文件
```
export GAZEBO_MODEL_PATH=~/catkin_ws/src/RoboND-Perception-Project/pr2_robot/models:$GAZEBO_MODEL_PATH
```

If you haven’t already, following line can be added to your .bashrc to auto-source all new terminals

如果您还没有，可以将以下行添加到.bashrc中以自动导出所有新终端
```
source ~/catkin_ws/devel/setup.bash
```

To run the demo:

运行演示：
```sh
$ cd ~/catkin_ws/src/RoboND-Perception-Project/pr2_robot/scripts
$ chmod u+x pr2_safe_spawner.sh
$ ./pr2_safe_spawner.sh
```
![demo-1](https://user-images.githubusercontent.com/20687560/28748231-46b5b912-7467-11e7-8778-3095172b7b19.png)

Once Gazebo is up and running, make sure you see following in the gazebo world:

一旦凉亭开始运行，请确保在凉亭世界中看到以下内容：

- Robot 机器人

- Table arrangement 桌上排列

- Three target objects on the table 桌子上的三个目标对象

- Dropboxes on either sides of the robot 机器人两边的收件箱

If any of these items are missing, please report as an issue on [the waffle board](https://waffle.io/udacity/robotics-nanodegree-issues).

如果任何这些项目丢失，请在  [the waffle board](https://waffle.io/udacity/robotics-nanodegree-issues) 上报告问题。

In your RViz window, you should see the robot and a partial collision map displayed:

在您的RViz窗口中，您将看到机器人和部分碰撞图显示：

![demo-2](https://user-images.githubusercontent.com/20687560/28748286-9f65680e-7468-11e7-83dc-f1a32380b89c.png)

Proceed through the demo by pressing the ‘Next’ button on the RViz window when a prompt appears in your active terminal

当您的活动终端出现提示时，按下RViz窗口上的 ‘Next’ 按钮继续演示

The demo ends when the robot has successfully picked and placed all objects into respective dropboxes (though sometimes the robot gets excited and throws objects across the room!)

当机器人成功拾取并将所有物体放置到相应的投影箱中时（尽管有时机器人会激动并将物体投射到房间内），演示结束！

Close all active terminal windows using **ctrl+c** before restarting the demo.

在重新启动演示之前，使用 **ctrl + c** 关闭所有活动的终端窗口。

You can launch the project scenario like this:

您可以像这样启动项目环境：
```sh
$ roslaunch pr2_robot pick_place_project.launch
```
# Required Steps for a Passing Submission:
1. Extract features and train an SVM model on new objects (see `pick_list_*.yaml` in `/pr2_robot/config/` for the list of models you'll be trying to identify). 
2. Write a ROS node and subscribe to `/pr2/world/points` topic. This topic contains noisy point cloud data that you must work with.
3. Use filtering and RANSAC plane fitting to isolate the objects of interest from the rest of the scene.
4. Apply Euclidean clustering to create separate clusters for individual items.
5. Perform object recognition on these objects and assign them labels (markers in RViz).
6. Calculate the centroid (average in x, y and z) of the set of points belonging to that each object.
7. Create ROS messages containing the details of each object (name, pick_pose, etc.) and write these messages out to `.yaml` files, one for each of the 3 scenarios (`test1-3.world` in `/pr2_robot/worlds/`).  See the example `output.yaml` for details on what the output should look like.  
8. Submit a link to your GitHub repo for the project or the Python code for your perception pipeline and your output `.yaml` files (3 `.yaml` files, one for each test world).  You must have correctly identified 100% of objects from `pick_list_1.yaml` for `test1.world`, 80% of items from `pick_list_2.yaml` for `test2.world` and 75% of items from `pick_list_3.yaml` in `test3.world`.
9. Congratulations!  Your Done!

# 通过提交的必需步骤：
1. 提取特征并在新对象上训练一个SVM模型（参见 `/pr2_robot/config/` 中的 `pick_list_*.yaml`，以获得您想要识别的模型列表）。
2. 编写一个ROS节点并订阅 `/pr2/world/points` 主题。此主题包含您必须使用的嘈杂点云数据。
3. 使用过滤和RANSAC平面拟合，将感兴趣的对象与场景的其余部分隔离开。
4. 应用欧几里德聚类，为单个项目创建单独的聚类。
5. 对这些对象执行对象识别，并为它们分配标签（RViz中的标记）。
6. 计算属于该对象的一组点的质心（x，y和z中的平均值）。
7. 创建包含每个对象的详细信息（名称，pick_pose等）的ROS消息，并将这些消息写入`.yaml`文件，对于3个场景中的每一个（`test23.world`在 `/pr2_robot/worlds/`）。有关输出应该如何的详细信息，请参见示例`output.yaml`。
8. 提交一个链接到项目的GitHub repo或者你的感知流水线的Python代码，输出`.yaml`文件（每个测试世界一个.`amam文件）。您必须从 `test1.world`的 `pick_list_1.yaml` 中正确识别出100％的对象，`test2.world` 的 `pick_list_2.yaml` 中有80％的项目和 `pick_list_3.yaml` 中的75％的项目`test3.world`。
9. 恭喜！你完成了！

# Extra Challenges: Complete the Pick & Place
7. To create a collision map, publish a point cloud to the `/pr2/3d_map/points` topic and make sure you change the `point_cloud_topic` to `/pr2/3d_map/points` in `sensors.yaml` in the `/pr2_robot/config/` directory. This topic is read by Moveit!, which uses this point cloud input to generate a collision map, allowing the robot to plan its trajectory.  Keep in mind that later when you go to pick up an object, you must first remove it from this point cloud so it is removed from the collision map!
8. Rotate the robot to generate collision map of table sides. This can be accomplished by publishing joint angle value(in radians) to `/pr2/world_joint_controller/command`
9. Rotate the robot back to its original state.
10. Create a ROS Client for the “pick_place_routine” rosservice.  In the required steps above, you already created the messages you need to use this service. Checkout the [PickPlace.srv](https://github.com/udacity/RoboND-Perception-Project/tree/master/pr2_robot/srv) file to find out what arguments you must pass to this service.
11. If everything was done correctly, when you pass the appropriate messages to the `pick_place_routine` service, the selected arm will perform pick and place operation and display trajectory in the RViz window
12. Place all the objects from your pick list in their respective dropoff box and you have completed the challenge!
13. Looking for a bigger challenge?  Load up the `challenge.world` scenario and see if you can get your perception pipeline working there!

＃额外的挑战：完成挑选
7.要创建碰撞映射，请将点云发布到 `/pr2/3d_map/points` 主题，并确保将 `/pr2/3d_map/points` 目录。该主题由Moveit！读取，它使用该点云输入来生成碰撞映射，允许机器人计划其轨迹。请记住，稍后当您拿起物体时，您必须首先将其从该点云中移除，以便从碰撞图中移除！
8.旋转机器人，生成桌面的碰撞图。这可以通过将联合角度值（以弧度表示）发布到 `/pr2/world_joint_controller/command` 来实现
9.将机器人旋转回原来的状态。
10.为 “pick_place_routine” rosservice创建一个ROS客户端。在上述所需步骤中，您已经创建了使用此服务所需的消息。检查[PickPlace.srv](https://github.com/udacity/RoboND-Perception-Project/tree/master/pr2_robot/srv) 文件，找出必须传递给此服务的参数。
11.如果一切正常，当您将适当的消息传递到 `pick_place_routine` 服务时，所选的臂将执行拾取和放置操作并在RViz窗口中显示轨迹
12.将所有从您的选择列表中的对象放在各自的退出框中，并完成挑战！
13. 寻找更大的挑战？加载 `challenge.world`  的场景，看看能否让你的感知管道在那里工作！

For all the step-by-step details on how to complete this project see the [RoboND 3D Perception Project Lesson](https://classroom.udacity.com/nanodegrees/nd209/parts/586e8e81-fc68-4f71-9cab-98ccd4766cfe/modules/e5bfcfbd-3f7d-43fe-8248-0c65d910345a/lessons/e3e5fd8e-2f76-4169-a5bc-5a128d380155/concepts/802deabb-7dbb-46be-bf21-6cb0a39a1961)
Note: The robot is a bit moody at times and might leave objects on the table or fling them across the room :D
As long as your pipeline performs succesful recognition, your project will be considered successful even if the robot feels otherwise!

有关如何完成此项目的所有分步细节，请参阅[RoboND 3D感知项目课程](https://classroom.udacity.com/nanodegrees/nd209/parts/586e8e81-fc68-4f71-9cab-98ccd4766cfe/modules/e5bfcfbd-3f7d-43fe-8248-0c65d910345a/lessons/e3e5fd8e-2f76-4169-a5bc-5a128d380155/concepts/802deabb-7dbb-46be-bf21-6cb0a39a1961)
注意：机器人有时会有点不舒服，可能会将物体放在桌子上或将它们甩在房间上：D
只要您的管道执行成功的认可，即使机器人感到不适，您的项目也将被认为是成功的！
