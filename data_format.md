## LiDAR
[velodyne64](ref3)
### pose file:
#### Format:
[frameId] [timeStamp] [pos0] [pos1] [pos2] [quat0] [quat1] [quat2] [quat3]

#### Example pose file (Dump from Apollo1.5_demo.bag):  
-------------21.pose begin----------------  
21 1504653361.853409 587261.755298 4141515.583796 -29.408425 0.015654 -0.005625 -0.117897 0.992887  
-------------21.pose end------------------  

#### Notes:
**File Name**:  
begin with 1.pose
* **frameId**:  
frame id, from 1
* **pos0**: ignore
* **pos1**: ignore
* **pos2**: ignore
* **quat0**:  
Quaternion 0
* **quat1**:  
Quaternion 1
* **quat2**:  
Quaternion 2
* **quat3**:  
Quaternion 3

### obstacles file:  
#### Format:
[frameId] [numOfObj]  
[objId] [trackId] [objType] [ctVelo0] [ctVelo1] [ctVelo2] [objLength] [objWidth] [objHeight] [theta] 0 0 [objVelocity0] [objVelocity1] [objVelocity2] [objCloudSize] ..._cloud point data_... [endl]

#### Example of obstacles file (Dump from Apollo1.5_demo.bag):  
-------------000020.txt begin----------------  
21 1
0 6 smallMot -53.46683058 25.45495444 -2.484871868 2.023377419 0.6376290917 0.6222643852 1.563876054 0 0 0 0 0 9 86733 86787 86841 86896 87278 87332 87384 87438 87980  
-------------000020.txt end------------------

#### Color of Obstacles by bounding boxes
[perception introduction](ref1)
1. smallMot: car (green)
2. nonMot: cyclist (blue)
3. pedestrain: pedestrian (pink)
4. unknown: unknown (purple)

#### Notes:
**File Name**:  
Six digits, begin with 000000.txt
* **frameId**:  
frame id, from 1, e.g. 000000.txt's is frame 1  
* **numOfObj**:  
quantity of objects per frame  
* **objId**:  
object id per frame, from 0
* **trackId**:  
object track id in the track list, from 0
* **objType**:  
pedestrain, smallMot, nonMot, unknown
* **ctVelo0**:  
center of objects:x, precision is 10
* **ctVelo1**:  
center of objects:y
* **ctVelo2**:  
center of objects:z min
* **objLength**:  
length of object box, size of main direction, unit M
* **objWidth**:  
width of object box, unit M
* **objHeight**:  
height of object box, unit M
* **theta**:  
the yaw angle, theta = 0.0 <=> direction = (1, 0, 0)
* **objVelocity0**:  
tracking velocity of object box:x
* **objVelocity1**:  
tracking velocity of object box:y
* **objVelocity2**:  
tracking velocity of object box:z
* **objCloudSize**:  
quantity of point cloud for the object
* **point data**:  
indices of resultant, int, nearest k search for 1
* **endl**:  
break, end of line

## Radar
Here is continental radar(another is ultrasonic radar), refers to [message](ref2)
topic name: /apollo/sensor/conti_radar data type: apollo::drivers::ContiRadar channel ID: CHANNEL_ID_ONE
### Radar Pose file
#### Format:
[frameId] [timeStamp] [pos0] [pos1] [pos2] [quat0] [quat1] [quat2] [quat3]

#### Example pose file (Dump from Apollo1.5_demo.bag):  
-------------1513807884.3730705.pose begin----------------  
789 1513807884.37307 587135.025097686 4141195.309519918 -31.79241004701922 -0.001084891613572836 0.007616758346557617 -0.1053255498409271 0.9944080710411072
-------------1513807884.3730705.pose end------------------  

#### Notes
same as pose file

### Radar Velocity file
#### Format:
[frameId] [timeStamp] [carLinearVelocityX] [carLinearVelocityY] [carLinearVelocityZ]
#### Example pose file (Dump from Apollo1.5_demo.bag):  
-------------1513807884.3730705.velocity begin----------------  
789 1513807884.37307 -0.07548268139362335 -0.1518359482288361 -0.016117874532938
-------------1513807884.3730705.velocity end------------------  

#### Notes
**File Name**:  
begin with timeStamp + .velocity
* **frameId**:  
frame id, is not from 1 (in 2.0 bag from 4), but continuous
* **timeStamp**:  
precision is less than file name
* **carLinearVelocityX**
car linear speed from localisation: x
* **carLinearVelocityY**
car linear speed from localisation: y
* **carLinearVelocityZ**
car linear speed from localisation: z

[ref1]: http://apollo.auto/platform/perception.html (Perception Introduction)
[ref2]: https://github.com/ApolloAuto/apollo/blob/master/modules/drivers/proto/conti_radar.proto (Radar Message)
[ref3]: https://github.com/ApolloAuto/apollo/blob/master/modules/drivers/velodyne/README_cn.md (velodyne64)
