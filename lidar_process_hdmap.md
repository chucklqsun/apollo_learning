Apollo感知处理系列(载入高精地图)
## 前言
感知模块的一个非常重要的用途就是判断并避开障碍物。Apollo依赖高精地图进行ROI划分，提高对道路上障碍物的判断精度，避免非行驶区域的物体干扰。本文是基于offline_lidar_visualizer_tool这个工具的源代码对Apollo如何查找车辆周围道路的进行解读。

## 相关参考文章
* [3D 障碍物感知][ref1]
+ [Apollo高精地图][ref2]
* [旋转矩阵与四元数][ref3]
* [POSE和PCD格式][ref4]

## 文件路径:
1. /modules/perception/obstacle/onboard/lidar_process.*
2. /modules/perception/obstacle/onboard/hdmap_input.*
3. /modules/map/hdmap/hdmap_util.*
4. /modules/map/hdmap/hdmap.*
5. /modules/map/hdmap/hdmap_impl.*

## 流水线:
1. **初始化高精地图**
2. 查询地图并获取关注区域ROI(Region of Interest)
2. 过滤关注区域
3. 切割出障碍物
4. 跟踪障碍物
5. 类型融合?

## 高精地图
### 读取车辆位置信息pose
```
Eigen::Matrix4d pose = Eigen::Matrix4d::Identity();
int frame_id = -1;
ReadPoseFile(pose_folder + pose_file_names[i], &pose, &frame_id,&time_stamp));
```
读取pose文件的坐标以及四元数(转换成了旋转矩阵)，作为仿射矩阵。
输出的pose格式如下
[[1−2(y^2+z^2), 2(xy-zw),       2(xz+yw),       X],
[2(xy+zw),      1-2(x^2+z^2),   2(yz-xw),       Y],
[2(xz-yw),      2(yz+xw),       1-2(x^2+y^2),   Z],
[0,             0,              0,              1]]

### 载入并初始化地图
```
hdmap_input_ = HDMapInput::instance();  //创建一个对象
hdmap_input_->Init();   //调用HDMap的LoadMapFromFile方法载入地图base_map和sim_map
```
单例对象HDMapInput，用互斥锁std::lock_guard<std::mutex>(在销毁或者抛出异常时候才会解锁)来保证载入地图的线程安全。

```
BuildLaneSegmentKDTree();
BuildJunctionPolygonKDTree();
BuildSignalSegmentKDTree();
BuildCrosswalkPolygonKDTree();
BuildStopSignSegmentKDTree();
BuildYieldSignSegmentKDTree();
BuildClearAreaPolygonKDTree();
BuildSpeedBumpSegmentKDTree();
```
使用OpendriveAdapter::LoadData载入地图数据，然后转换成KDTree。

```
auto velodyne_trans = std::make_shared<Eigen::Matrix4d>(pose);

HdmapStructPtr hdmap = nullptr;
if (hdmap_input_) {
  PointD velodyne_pose = {0.0, 0.0, 0.0, 0};  // (0,0,0)
  Affine3d temp_trans(*velodyne_trans); //pose做成仿射矩阵
  PointD velodyne_pose_world = pcl::transformPoint(velodyne_pose, temp_trans);
  hdmap.reset(new HdmapStruct);
  hdmap_input_->GetROI(velodyne_pose_world, FLAGS_map_radius, &hdmap);
  PERF_BLOCK_END("lidar_get_roi_from_hdmap");
}
```
定义velodyne_pose为PointD类型，设定位置是车辆坐标系的中心(0,0,0)。此类型如下，坐标x,y,z以及强度
```
// using double type to define x, y, z.
struct PointD {
  double x;
  double y;
  double z;
  uint8_t intensity;
  EIGEN_MAKE_ALIGNED_OPERATOR_NEW
} EIGEN_ALIGN16;
```
通过pcl::transformPoint用车辆的仿射矩阵来转换激光雷达的位置成世界坐标系中的位置

### 扫描半径获得ROI
```
auto* hdmap = HDMapUtil::BaseMapPtr();  //使用base地图
common::PointENU point;
point.set_x(pointd.x);
point.set_y(pointd.y);
point.set_z(pointd.z);
hdmap->GetRoadBoundaries(point,map_radius, &boundary_vec,&junctions_vec);   //获得路边界
MergeBoundaryJunction(boundary_vec, junctions_vec, mapptr)  //合并交叉路
```
GetRoadBoundaries调用HDMapImpl::GetLane,并且去掉重复的road+section和junction。如果没找到路就报错。  
交叉路是从land里面提炼出来的id，进行合并。

```
kdtree.GetObjects(center, radius);
```
对KdTree扫描半径(map_radius参数，默认70米)获取雷达周围的对象，输出给HdmapStructPrt。

### 输出HdmapStructPtr指针对象
```
typedef ::pcl::PointCloud<PointD> PointDCloud;
typedef pcl_util::PointDCloud PolygonDType;

struct alignas(16) RoadBoundary {
  PolygonDType left_boundary;   //左边界
  PolygonDType right_boundary;  //右边界
};

struct alignas(16) HdmapStruct {
  std::vector<RoadBoundary> road_boundary;  //路边界
  std::vector<PolygonDType> junction;       //交叉口
};

typedef std::shared_ptr<HdmapStruct> HdmapStructPtr;
```
后续就是进行ROI过滤，获得对应的区域索引，复制出对应的云点给后续CNN切割。
```
if (roi_filter_->Filter(point_cloud, roi_filter_options,
    roi_indices.get())) {
        pcl::copyPointCloud(*point_cloud, *roi_indices, *roi_cloud);
    roi_indices_ = roi_indices;
}
```

## 其他
有时间会写一下KD树原理和如何进行ROI过滤。

[ref1]: https://github.com/ApolloAuto/apollo/blob/master/docs/specs/3d_obstacle_perception_cn.md "3D 障碍物感知"
[ref2]: https://github.com/chucklqsun/apollo_learning/blob/master/opendrive_adapter.md "Apollo高精地图(hdmap)"
[ref3]: http://insaneguy.me/2015/03/25/rotation_matrix_and_quaternions/ "旋转矩阵与四元数"
[ref4]: https://github.com/chucklqsun/apollo_learning/blob/master/data_format.md "Data Format"
