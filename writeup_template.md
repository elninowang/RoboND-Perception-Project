## Project: Perception Pick & Place 项目：感知拿起&放下
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

###编写模板：如果要将其作为一个缩写文件提交，您可以将此文件用作写作模板，但如果愿意，可以随意使用其他方法并提交pdf。

---


# Required Steps for a Passing Submission:
1. Extract features and train an SVM model on new objects (see `pick_list_*.yaml` in `/pr2_robot/config/` for the list of models you'll be trying to identify). 
2. Write a ROS node and subscribe to `/pr2/world/points` topic. This topic contains noisy point cloud data that you must work with.
3. Use filtering and RANSAC plane fitting to isolate the objects of interest from the rest of the scene.
4. Apply Euclidean clustering to create separate clusters for individual items.
5. Perform object recognition on these objects and assign them labels (markers in RViz).
6. Calculate the centroid (average in x, y and z) of the set of points belonging to that each object.
7. Create ROS messages containing the details of each object (name, pick_pose, etc.) and write these messages out to `.yaml` files, one for each of the 3 scenarios (`test1-3.world` in `/pr2_robot/worlds/`).  [See the example `output.yaml` for details on what the output should look like.](https://github.com/udacity/RoboND-Perception-Project/blob/master/pr2_robot/config/output.yaml)  
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

＃额外的挑战：完成 拿去&放下
7.要创建碰撞映射，请将点云发布到 `/pr2/3d_map/points` 主题，并确保将 `/pr2/3d_map/points` 目录。该主题由Moveit！读取，它使用该点云输入来生成碰撞映射，允许机器人计划其轨迹。请记住，稍后当您拿起物体时，您必须首先将其从该点云中移除，以便从碰撞图中移除！
8.旋转机器人，生成桌面的碰撞图。这可以通过将联合角度值（以弧度表示）发布到 `/pr2/world_joint_controller/command` 来实现
9.将机器人旋转回原来的状态。
10.为 “pick_place_routine” rosservice创建一个ROS客户端。在上述所需步骤中，您已经创建了使用此服务所需的消息。检查[PickPlace.srv](https://github.com/udacity/RoboND-Perception-Project/tree/master/pr2_robot/srv) 文件，找出必须传递给此服务的参数。
11.如果一切正常，当您将适当的消息传递到 `pick_place_routine` 服务时，所选的臂将执行拾取和放置操作并在RViz窗口中显示轨迹
12.将所有从您的选择列表中的对象放在各自的退出框中，并完成挑战！
13. 寻找更大的挑战？加载 `challenge.world`  的场景，看看能否让你的感知管道在那里工作！

## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

编写模板：如果要将其作为一个缩写文件提交，您可以将此文件用作写作模板，但如果愿意，可以随意使用其他方法并提交PDF格式。

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

#### 1.提供一个包含所有标题点的写入/自述文件，以及如何处理每个要点。 您可以提交您的写作作为降价或pdf。

You're reading it!

### Exercise 1, 2 and 3 pipeline implemented 练习1,2和3管道实施
#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.

完成练习1步。 实施过滤管道和RANSAC平面配管。

#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  

完成练习2步：管道包括聚类进行分段实现。

#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
Here is an example of how to include an image in your writeup.

完成练习3步。 提取的特征和SVM训练。 实现对象识别。

![demo-1](https://user-images.githubusercontent.com/20687560/28748231-46b5b912-7467-11e7-8778-3095172b7b19.png)

### Pick and Place Setup 拿起和放下设置

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.

对于所有三个桌面设置 (`test*.world`)，执行对象识别，然后读入相应的选择列表(`pick_list_*.yaml`)。 构造下一个包含一个有效的 `PickPlace` 请求的消息，将它们输出到`.yaml`格式。

And here's another image!  这是另一个图片！
![demo-2](https://user-images.githubusercontent.com/20687560/28748286-9f65680e-7468-11e7-83dc-f1a32380b89c.png)

Spend some time at the end to discuss your code, what techniques you used, what worked and why, where the implementation might fail and how you might improve it if you were going to pursue this project further.  

花一些时间来讨论您的代码，使用哪些技术，什么工作以及为什么实施可能会失败，以及如何进一步改进此项目。
