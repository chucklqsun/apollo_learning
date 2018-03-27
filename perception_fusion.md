pollo感知处理系列(感知融合)

## 前言
Apollo障碍物感知包括基于LiDAR和基于雷达的障碍物感知，融合两者的结果。一般来说，它用来管理和关联不同的传感器的结果，通过Kalman过滤器整合障碍物速度。

1. 基于LiDAR的障碍物感知，是用全卷积深度神经网络，预测障碍物的属性，比如说前景概率，物体中心的位移和物体分类概率。基于这些属性进行物体的分割。
2. 基于雷达的障碍物感知用阿里处理原始雷达数据。一般来说，它提供track id，移除噪音，建立障碍物结果和用ROI过滤结果。

## offline_sequential_obstacle_perception_visualizer 和 offline_perception_visualizer 区别
1. 前者可以选择fuse，也可以单独选择Lidar或者Radar的结果.
2. 后者可以保存results，前者不行（需要代码改造）
3. bag里有两个摄像头的image数据，是否有可能做到三者融合？(如何导出image数据)
4. 前者是以timestamp作为文件名，后者是frame id作为文件名
5. Radar数据还需要进一步研究其格式

## 相关参考文章
1. [感知介绍](ref1)
2. [如何运行离线融合障碍物感知可视化工具](ref2)

[ref1]: http://apollo.auto/platform/perception.html "Apollo Perception Introduction"
[ref2]: https://github.com/ApolloAuto/apollo/blob/master/docs/howto/how_to_run_offline_sequential_obstacle_perception_visualizer.md "offline sequential obstacle perception visualizer"
