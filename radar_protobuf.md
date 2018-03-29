# Apollo雷达protobuf详解

### protobuf在项目中的位置
modules/drivers/proto/conti_radar.proto

### ContiRadar
```
//头信息  
optional common.Header header = 1;  
//雷达探测到的障碍物数组  
repeated ContiRadarObs contiobs = 2;  //conti radar obstacle array  
optional RadarState_201 radar_state = 3;  
optional ClusterListStatus_600 cluster_list_status = 4;  
optional ObjectListStatus_60A object_list_status = 5;  
```

### common.Header
```
//来自ros::Time::now(), 在调用SerializeToString() and publish()前一刻使用
timestamp_sec: 1513807857.6542

//continental radar
module_name: "conti_radar"

//每个消息的序列号.每个模块维护它自己的序列号计数器，启动后从1开始
sequence_num: 13417

//雷达传感器时间戳(nano-second)
radar_timestamp: 1513807857654200064
```
### ContiRadarObs [数组]
```
//                x axis  ^
//                        | longitude_dist
//                        |
//                        |
//                        |
//          lateral_dist  |
//          y axis        |
//        <----------------
//        ooooooooooooooooo <-雷达前表面
header {
  //每个扫到的反射点时间戳不一样
  timestamp_sec: 1513807857.7242026
  //固定模块名字conti_radar
  module_name: "conti_radar"
  //序列号是一致的
  sequence_num: 13417
}
//雷达操作模式：
//Cluster模式表示位置和移动相似的反射信号。每个Cluster提供一组信息
//Track模式相比于Cluster模式多了历史。他们代表一段时间的跟踪Cluster(所以除了位置信息，还会有纵向和横向的速度)。
//false=track, true=cluster
clusterortrack: false

//障碍物编号
obstacle_id: 2482

//纵向距离 (+) = 前方; 单位 = 米
longitude_dist: 183.4000000000001
//横向距离 (+) = 左方; 单位 = 米
lateral_dist: 14.400000000000006

//纵向速度 (+) = 向前; 单位 = 米/秒
longitude_vel: -8.25
//横向速度 (+) = 向左; 单位 = 米/秒
lateral_vel: 0.25

//障碍物反射截面，单位dBsm
rcs: 3.5

//运动类型
// 0 = moving, 1 = stationary, 2 = oncoming, 3 = stationary candidate
// 4 = unkonwn, 5 = crossing stationary, 6 = crossing moving, 7 = stopped
dynprop: 2

//纵向距离标准偏差
longitude_dist_rms: 0.478
//横向距离标准偏差
lateral_dist_rms: 1.023

//纵向速度标准偏差
longitude_vel_rms: 0.616
//横向速度标准偏差
lateral_vel_rms: 0.794

//障碍物存在概率
probexist: 0.999

//以下是只有跟踪模式才有的信息

//当前测量状态 0 = deleted, 1 = new, 2 = measured, 3 = predicted, 4 = deleted for, 5 = new from merge
meas_state: 2

//纵向加速度
longitude_accel: 1.4600000000000009

//横向加速度
lateral_accel: 0.0

//相对于雷达的定位角; (+) = 逆时针; 单位 = 度
oritation_angle: 3.200000000000017

//纵向加速度均方根
longitude_accel_rms: 1.023

//横向加速度均方根
lateral_accel_rms: 0.005

//定位角均方根
oritation_angle_rms: 0.332

//长度；单位: 米
length: 4.4
//宽度；单位: 米
width: 1.8

//障碍物类型
// 0: point; 1: car; 2: truck; 3: pedestrian; 4: motocycle; 5: bicycle; 6: wide; 7: unknown
obstacle_class: 1



header {
  timestamp_sec: 1513807857.724203
  module_name: "conti_radar"
  sequence_num: 13417
}
clusterortrack: false
obstacle_id: 2389
longitude_dist: 33.80000000000007
lateral_dist: 17.600000000000023
longitude_vel: -8.5
lateral_vel: -0.5
rcs: 8.0
dynprop: 2
longitude_dist_rms: 0.371
lateral_dist_rms: 0.616
longitude_vel_rms: 0.478
lateral_vel_rms: 0.794
probexist: 0.75
meas_state: 2
longitude_accel: 1.6400000000000006
lateral_accel: 0.0
oritation_angle: -3.1999999999999886
longitude_accel_rms: 1.023
lateral_accel_rms: 0.005
oritation_angle_rms: 0.332
length: 4.4
width: 1.8
obstacle_class: 1
...
...
...
```
