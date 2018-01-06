# Apollo高精地图(hdmap)
## 相关参考文章
* [从百度Apollo代码谈高精度地图][ref1]
* [Apollo坐标系统][ref2]

## 文件路径:
1. /modules/map/hdmap/adapter/opendrive_adapter.*
1. /modules/map/hdmap/adapter/proto_organizer.*
2. /modules/map/proto/*

## 流水线:
1. 将地图文件作为XML文件载入
2. 抽取头部信息（见下文头结构）
3. 抽取路信息
 - 查看是否是交叉口id
 - 抽取车道信息
 - 抽取物体信息（停止线，人行横道，禁停区，减速带）
 - 抽取信号标示（信号灯，停车标示，让车标示）
4. 抽取交叉口信息（特别的是，Apollo格式和OpenDrive不一样）
 - outline
 - objectOverlapGroup
    - objectReference: id
5. 通过分析路和交叉口，遍历所有道，抽取重叠元素
 - 找重叠的物体（4类）
 - 找重叠的信号标示（3类）
 - 找重叠的车道(起点，终点，是否合并)
6. 本阶段地图准备就绪

**注意**
* 通过地图东和西的最远位置来判断时区是否一致，不支持跨时区地图。
* 通过对地图geoReference字段分析，转换成UTM投影(60等分，每个等分投射成二维直角坐标系)。

## 地图头结构
```
message Header {
  optional bytes version = 1;       //地图数据库的版本
  optional bytes date = 2;          //地图数据库创建的时间
  optional Projection projection = 3;   //投射坐标系:"+proj=utm +zone=时区 +ellps=WGS84 +datum=WGS84 +units=m +no_defs";
  optional bytes district = 4;      //地图数据库名称
  optional bytes generation = 5;    //???
  optional bytes rev_major = 6; //OpenDrive格式的主要版本号
  optional bytes rev_minor = 7; //OpenDrive格式的次要版本号
  optional double left = 8;     //west，最小惯性参考系x值
  optional double top = 9;      //north，最大惯性参考系y值
  optional double right = 10;   //east，最大惯性参考系x值
  optional double bottom = 11;  //south，最小惯性参考系y值
  optional bytes vendor = 12;   //地图厂商名称
}
```

## 地图数据结构
```
message Map {
  optional Header header = 1;

  repeated Crosswalk crosswalk = 2;     //人行横道
  repeated Junction junction = 3;       //交叉口
  repeated Lane lane = 4;               //车道
  repeated StopSign stop_sign = 5;      //停车标志
  repeated Signal signal = 6;           //信号灯
  repeated YieldSign yield = 7;         //让车标志
  repeated Overlap overlap = 8;         //重叠
  repeated ClearArea clear_area = 9;    //禁停区
  repeated SpeedBump speed_bump = 10;   //减速带
  repeated Road road = 11;              //路，包含道路，物体等信息
}
```

## 信号灯（v2.0新增信号灯感知）
### 数据结构
```
message Subsignal {
  enum Type {
    UNKNOWN = 1;        //未知
    CIRCLE = 2;         //圆圈
    ARROW_LEFT = 3;     //向左箭头
    ARROW_FORWARD = 4;  //向前进箭头
    ARROW_RIGHT = 5;    //向右箭头
    ARROW_LEFT_AND_FORWARD = 6;      //向左前进箭头
    ARROW_RIGHT_AND_FORWARD = 7;     //向右前进箭头
    ARROW_U_TURN = 8;           //U型箭头
  };

  optional Id id = 1;       //编号
  optional Type type = 2;   //类型

  // Location of the center of the bulb. now no data support.
  // 中间灯泡的位置
  optional apollo.common.PointENU location = 3;
}
message Signal {
  enum Type {
    UNKNOWN = 1;    //未知
    MIX_2_HORIZONTAL = 2;   //水平2灯
    MIX_2_VERTICAL = 3;     //垂直2灯
    MIX_3_HORIZONTAL = 4;   //水平3灯
    MIX_3_VERTICAL = 5;     //垂直3灯
    SINGLE = 6;
  };

  optional Id id = 1;               //编号
  optional Polygon boundary = 2;    //轮廓
  repeated Subsignal subsignal = 3; //子信号信息
  // TODO: add orientation. now no data support.对应行驶方向，目前无数据
  repeated Id overlap_id = 4; //重叠id
  optional Type type = 5;   //信号灯外形类型，见Type
  // stop line
  repeated Curve stop_line = 6; //车辆停止线位置
}
```
### 轮廓
如下，由坐标给出轮廓位置。Apollo为其outlien定义了cornerGlobal元素，四个角，具体可见 [traffic_light_perception_cn][ref3]。有空写一下信号灯感知的具体代码实现。

```
message Polygon {
  repeated apollo.common.PointENU point = 1;
}

message PointENU {
  optional double x = 1 [default = nan];  // East from the origin, in meters.
  optional double y = 2 [default = nan];  // North from the origin, in meters.
  optional double z = 3 [default = 0.0];  // Up from the WGS-84 ellipsoid, in
                                          // meters.
}
```

## 其他
Apollo自己定制了地图数据结构，但是我也没找到合适的工具能打开查看并且编辑他们家地图，希望了解这方面工具的同学能提供帮助。

[ref1]: https://zhuanlan.zhihu.com/p/30728273 "从百度Apollo代码谈高精度地图"
[ref2]: https://github.com/ApolloAuto/apollo/blob/master/docs/specs/coordination.pdf "Apollo坐标系统"
[ref3]: https://github.com/chucklqsun/apollo_learning/blob/master/traffic_light_perception_cn.md "traffic light perception"
