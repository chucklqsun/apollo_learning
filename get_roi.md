Apollo感知处理系列(获取ROI区域)

## 前言
Apollo在判断将LiDAR点云进行分割识别成障碍物之前，会先进行ROI的过滤，以避免认出道路外的物体。这种区域的定义是基于在一个半径内HDMap的道路对照后过滤。

## 相关参考文章
1. [官方参考文章](ref1)

## 文件路径
1. /modules/perception/obstacle/onboard/hdmap_input.cc
2. /modules/perception/obstacle/onboard/lidar_process.cc
3. /modules/perception/obstacle/lidar/roi_filter/hdmap_roi_filter/hdmap_roi_filter.cc

## GetROI
来看一下调用:
```
hdmap_input_->GetROI(velodyne_pose_world, FLAGS_map_radius, &hdmap);
```
第一个传入参数PointD是LiDAR的世界坐标,第二个是ROI半径，第三个是HDMap

```
int status = hdmap->GetRoadBoundaries(point, map_radius, &boundary_vec,
                                      &junctions_vec);
```
根据半径，获取点周围kdtree上的道路和交叉口。如果没有找到就报错。
```
MergeBoundaryJunction(boundary_vec, junctions_vec, mapptr)
```
合并道路和交叉口。(按照一定的step进行down sample)
```
MergeHdmapStructToPolygons(roi_filter_options.hdmap, &polygons);
TransformFrame(cloud, temp_trans, polygons, &polygons_local, cloud_local);
```
把HDMap里的道路信息合并转换成多边形，并且把点云和多边形点都转换成frame的本地坐标
```
GetMajorDirection
```
比较x的范围和y范围，取小的那个为主方向来生成bitmap格子
```
DrawPolygonInBitmap(raw_polygons, extend_dist_, &bitmap);
```
根据道路的多边形来定义bitmap的0和1
```
Bitmap2dFilter
```
把每个点放进bitmap里判断是否在ROI区域内

下面是关键代码:
```
bool HDMapInput::GetROI(const PointD& pointd, const double& map_radius, HdmapStructPtr* mapptr) {
  auto* hdmap = HDMapUtil::BaseMapPtr();
  std::unique_lock<std::mutex> lock(mutex_);
  if (mapptr != NULL && *mapptr == nullptr) {
    (*mapptr).reset(new HdmapStruct);
  }
  common::PointENU point;
  point.set_x(pointd.x);
  point.set_y(pointd.y);
  point.set_z(pointd.z);
  std::vector<RoadROIBoundaryPtr> boundary_vec;
  std::vector<JunctionBoundaryPtr> junctions_vec;

  int status = hdmap->GetRoadBoundaries(point, map_radius, &boundary_vec,
                                        &junctions_vec);
  if (!MergeBoundaryJunction(boundary_vec, junctions_vec, mapptr).ok()) {
    AERROR << "merge boundary and junction to hdmap struct failed.";
    return false;
  }
  return true;
}

//ROI算法插件
PointCloudPtr roi_cloud(new PointCloud);
if (roi_filter_ != nullptr) {
  PointIndicesPtr roi_indices(new PointIndices);    //ROI点云的index数组
  ROIFilterOptions roi_filter_options;
  roi_filter_options.velodyne_trans = velodyne_trans;   //用来转换成LiDAR本地坐标
  roi_filter_options.hdmap = hdmap;     //高精地图
  if (roi_filter_->Filter(point_cloud, roi_filter_options, roi_indices.get())) {
    pcl::copyPointCloud(*point_cloud, *roi_indices, *roi_cloud);
    roi_indices_ = roi_indices;
  }
}


bool HdmapROIFilter::Filter(const pcl_util::PointCloudPtr& cloud,
                            const ROIFilterOptions& roi_filter_options,
                            pcl_util::PointIndices* roi_indices) {
  Eigen::Affine3d temp_trans(*(roi_filter_options.velodyne_trans));

  std::vector<PolygonDType> polygons;
  MergeHdmapStructToPolygons(roi_filter_options.hdmap, &polygons);

  // 1. Transform polygon and point to local coordinates
  pcl_util::PointCloudPtr cloud_local(new pcl_util::PointCloud);
  std::vector<PolygonType> polygons_local;
  TransformFrame(cloud, temp_trans, polygons, &polygons_local, cloud_local);

  return FilterWithPolygonMask(cloud_local, polygons_local, roi_indices);
}


bool HdmapROIFilter::FilterWithPolygonMask(
    const pcl_util::PointCloudPtr& cloud,
    const std::vector<PolygonType>& map_polygons,
    pcl_util::PointIndices* roi_indices) {
  // 2. Get Major Direction as X direction and convert map_polygons to raw
  // polygons
  std::vector<PolygonScanConverter::Polygon> raw_polygons(map_polygons.size());
  MajorDirection major_dir = GetMajorDirection(map_polygons, &raw_polygons);

  // 3. Convert polygons into roi grids in bitmap
  Eigen::Vector2d min_p(-range_, -range_);
  Eigen::Vector2d max_p(range_, range_);
  Eigen::Vector2d grid_size(cell_size_, cell_size_);
  Bitmap2D bitmap(min_p, max_p, grid_size, major_dir);
  bitmap.BuildMap();

  DrawPolygonInBitmap(raw_polygons, extend_dist_, &bitmap);

  // 4. Check each point whether is in roi grids in bitmap
  return Bitmap2dFilter(cloud, bitmap, roi_indices);
}

//bool HdmapROIFilter::Bitmap2dFilter
if (bitmap.IsExist(p) && bitmap.Check(p)) {
      roi_indices_ptr->indices.push_back(i);
}
```
[ref1]: https://github.com/ApolloAuto/apollo/blob/master/docs/specs/3d_obstacle_perception_cn.md "3D Obstacle Perception"
