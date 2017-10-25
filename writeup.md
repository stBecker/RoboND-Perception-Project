## Project: Perception Pick & Place
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

[//]: # (Image References)

[labels]: ./writeup/labels_1.PNG
[cluster]: ./writeup/pcl_cluster.PNG
[objects]: ./writeup/pcl_objects.PNG
[table]: ./writeup/pcl_table.PNG
[image1]: ./writeup/labels_1.PNG
[conf1]: ./writeup/confusion_1.PNG
[conf2]: ./writeup/confusion_2.PNG
[conf3]: ./writeup/confusion_3.PNG
[world1]: ./writeup/world1.PNG
[world1_2]: ./writeup/world1_2.PNG
[world2]: ./writeup/world2.PNG
[world2_2]: ./writeup/world2_2.PNG
[world2_3]: ./writeup/world2_3.PNG
[world3]: ./writeup/world3.PNG
[world3_2]: ./writeup/world3_2.PNG



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

# Extra Challenges: Complete the Pick & Place
7. To create a collision map, publish a point cloud to the `/pr2/3d_map/points` topic and make sure you change the `point_cloud_topic` to `/pr2/3d_map/points` in `sensors.yaml` in the `/pr2_robot/config/` directory. This topic is read by Moveit!, which uses this point cloud input to generate a collision map, allowing the robot to plan its trajectory.  Keep in mind that later when you go to pick up an object, you must first remove it from this point cloud so it is removed from the collision map!
8. Rotate the robot to generate collision map of table sides. This can be accomplished by publishing joint angle value(in radians) to `/pr2/world_joint_controller/command`
9. Rotate the robot back to its original state.
10. Create a ROS Client for the “pick_place_routine” rosservice.  In the required steps above, you already created the messages you need to use this service. Checkout the [PickPlace.srv](https://github.com/udacity/RoboND-Perception-Project/tree/master/pr2_robot/srv) file to find out what arguments you must pass to this service.
11. If everything was done correctly, when you pass the appropriate messages to the `pick_place_routine` service, the selected arm will perform pick and place operation and display trajectory in the RViz window
12. Place all the objects from your pick list in their respective dropoff box and you have completed the challenge!
13. Looking for a bigger challenge?  Load up the `challenge.world` scenario and see if you can get your perception pipeline working there!

## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Exercise 1, 2 and 3 pipeline implemented
#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.

The pipeline consists of the following steps:
1. Voxel Grid Downsampling
2. Statistical Outlier Filtering
3. PassThrough Filter for z and y axis
4. RANSAC Plane Segmentation
5. Another round of Statistical Outlier Filtering

![alt text][table]


![alt text][objects]

#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented. 

The clustering pipeline consists of the following steps:
1. Euclidean Clustering
2. Create Cluster-Mask Point Cloud

![alt text][cluster]


#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.

For the color and normals histograms I used 64 bins for each channel, so 6*64 or 384 features.
The linear SVM kernel was fittet with training data from 200 observation of each object, 
for each collection of objects seperately.

![alt text][labels]


### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.

##### Confusion matrices for the 3 worlds after 200 training images for each object

![alt text][conf1]

![alt text][conf2]

![alt text][conf3]


##### Running the whole pipeline, including collision detection of the table and calling the pick_place_routine


![alt text][world1]

![alt text][world1_2]

![alt text][world2]

![alt text][world2_2]

![alt text][world2_3]

![alt text][world3]

![alt text][world3_2]


At first I ran into problems with the object detection accuracy, when I only used 25 observations per object for the classifier training.
I tried several different values for the number of captured features and finally arrived at 200 observations per object, 
which gave satisfactory results.

Currently the collision detection only captures the table; as a future improvement I should also add the remaining objects as collision objects.


