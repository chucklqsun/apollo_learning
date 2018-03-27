### Command
rosbag info example.bag
### demo-sensor-demo-apollo-1.5.bag
```
path:        ./data/bag/demo-sensor-demo-apollo-1.5.bag
version:     2.0
duration:    2:16s (136s)
start:       Sep 06 2017 09:16:00.61 (1504653360.61)
end:         Sep 06 2017 09:18:17.51 (1504653497.51)
size:        8.9 GB
messages:    265972
compression: none [2867/2867 chunks]
types:       bond/Status                       [eacc84bf5d65b6777d4c50f463dfb9c8]
             pb_msgs/ADCTrajectory             [97587fe9a5b2df2b61888d56c6fc697b]
             pb_msgs/Chassis                   [d6a21658031a6a4615858d76f8b5178e]
             pb_msgs/ControlCommand            [67f7ff8a4c675dc97a8c7ce6d6289943]
             pb_msgs/GnssStatus                [6ab9bfa7e56e2724f6d30280b731fef2]
             pb_msgs/Gps                       [8fad5985ce947d3b6854fd093a59c429]
             pb_msgs/Imu                       [bdef0ba51869607ed95736d41e80c1f5]
             pb_msgs/InsStat                   [36306149a641468d85afa4cf44de7141]
             pb_msgs/InsStatus                 [e5242c64749c5980f75940dffcdf2fba]
             pb_msgs/LocalizationEstimate      [503c8e75900db180bc61534806a37cfb]
             pb_msgs/PerceptionObstacles       [c6fd886a685be1dbbc6174bbc5a754de]
             pb_msgs/PredictionObstacles       [45bac0c01020cbf041fb9bd39f790e93]
             pb_msgs/StreamStatus              [d6e98995ddcddd79bc955699f0ac28c9]
             rosgraph_msgs/Log                 [acffd30cd6b6de30f120938c17c593fb]
             sensor_msgs/PointCloud2           [1158d486dd51d683ce2f1be655c3c181]
             std_msgs/String                   [992ce8a1687cec8c8bd883ec73ca41d1]
             tf2_msgs/TFMessage                [94810edda583a504dfda3829e70d7eec]
             velodyne_msgs/VelodyneScanUnified [a02f26cda99b9e0189aac08ed1065a71]
topics:      /apollo/canbus/chassis                              13439 msgs    : pb_msgs/Chassis                  
             /apollo/control                                     12107 msgs    : pb_msgs/ControlCommand           
             /apollo/localization/pose                           13456 msgs    : pb_msgs/LocalizationEstimate     
             /apollo/perception/obstacles                         1257 msgs    : pb_msgs/PerceptionObstacles      
             /apollo/planning                                      630 msgs    : pb_msgs/ADCTrajectory            
             /apollo/prediction                                   1242 msgs    : pb_msgs/PredictionObstacles      
             /apollo/sensor/gnss/corrected_imu                   13114 msgs    : pb_msgs/Imu                      
             /apollo/sensor/gnss/gnss_status                       543 msgs    : pb_msgs/GnssStatus               
             /apollo/sensor/gnss/ins_stat                          270 msgs    : pb_msgs/InsStat                  
             /apollo/sensor/gnss/ins_status                          1 msg     : pb_msgs/InsStatus                
             /apollo/sensor/gnss/odometry                        13472 msgs    : pb_msgs/Gps                      
             /apollo/sensor/gnss/raw_data                        40676 msgs    : std_msgs/String                  
             /apollo/sensor/gnss/stream_status                       1 msg     : pb_msgs/StreamStatus             
             /apollo/sensor/velodyne64/PointCloud2                1321 msgs    : sensor_msgs/PointCloud2          
             /apollo/sensor/velodyne64/VelodyneScanUnified        1322 msgs    : velodyne_msgs/VelodyneScanUnified
             /apollo/sensor/velodyne64/compensator/PointCloud2    1307 msgs    : sensor_msgs/PointCloud2          
             /gnss_nodelet_manager/bond                            817 msgs    : bond/Status                       (4 connections)
             /rosout                                             73261 msgs    : rosgraph_msgs/Log                 (12 connections)
             /rosout_agg                                         63356 msgs    : rosgraph_msgs/Log                
             /tf                                                 13568 msgs    : tf2_msgs/TFMessage               
             /tf_static                                              1 msg     : tf2_msgs/TFMessage               
             /velodyne_nodelet_manager/bond                        811 msgs    : bond/Status                       (4 connections)
```

### demo-sensor-apollo-2.0: 2018-01-03-19-37-16.bag
```
path:        ./data/bag/2018-01-03-19-37-16.bag
version:     2.0
duration:    1:00s (60s)
start:       Jan 03 2018 22:37:30.31 (1514979450.31)
end:         Jan 03 2018 22:38:31.21 (1514979511.21)
size:        5.4 GB
messages:    49717
compression: none [1528/1528 chunks]
types:       pb_msgs/Chassis              [d6a21658031a6a4615858d76f8b5178e]
             pb_msgs/ContiRadar           [cc92608f43dabc2a96119eaca0c19535]
             pb_msgs/EpochObservation     [6d088c32c2d00b8a760974f0c580dfb7]
             pb_msgs/GnssBestPose         [c4465e2257bee53c91729e53c69bf7c5]
             pb_msgs/GnssEphemeris        [aa53419c1014e11a3b4604e3c54daf7b]
             pb_msgs/GnssStatus           [6ab9bfa7e56e2724f6d30280b731fef2]
             pb_msgs/Gps                  [8fad5985ce947d3b6854fd093a59c429]
             pb_msgs/Imu                  [bdef0ba51869607ed95736d41e80c1f5]
             pb_msgs/InsStat              [36306149a641468d85afa4cf44de7141]
             pb_msgs/LocalizationEstimate [503c8e75900db180bc61534806a37cfb]
             sensor_msgs/Image            [060021388200f6f0f447d0fcd9c64743]
             sensor_msgs/PointCloud2      [1158d486dd51d683ce2f1be655c3c181]
             tf2_msgs/TFMessage           [94810edda583a504dfda3829e70d7eec]
topics:      /apollo/canbus/chassis                               5851 msgs    : pb_msgs/Chassis             
             /apollo/localization/pose                            5856 msgs    : pb_msgs/LocalizationEstimate
             /apollo/sensor/camera/traffic/image_long              471 msgs    : sensor_msgs/Image           
             /apollo/sensor/camera/traffic/image_short             469 msgs    : sensor_msgs/Image           
             /apollo/sensor/conti_radar                            789 msgs    : pb_msgs/ContiRadar          
             /apollo/sensor/gnss/best_pose                          59 msgs    : pb_msgs/GnssBestPose        
             /apollo/sensor/gnss/corrected_imu                    5838 msgs    : pb_msgs/Imu                 
             /apollo/sensor/gnss/gnss_status                        59 msgs    : pb_msgs/GnssStatus          
             /apollo/sensor/gnss/imu                             11630 msgs    : pb_msgs/Imu                 
             /apollo/sensor/gnss/ins_stat                          118 msgs    : pb_msgs/InsStat             
             /apollo/sensor/gnss/odometry                         5848 msgs    : pb_msgs/Gps                 
             /apollo/sensor/gnss/rtk_eph                            49 msgs    : pb_msgs/GnssEphemeris       
             /apollo/sensor/gnss/rtk_obs                           352 msgs    : pb_msgs/EpochObservation    
             /apollo/sensor/velodyne64/compensator/PointCloud2     587 msgs    : sensor_msgs/PointCloud2     
             /tf                                                 11740 msgs    : tf2_msgs/TFMessage          
             /tf_static                                              1 msg     : tf2_msgs/TFMessage
```

### Notice
1. export_sensor tool has time sequential feature and it must be launched with the bag playing.
2. folder lidar and radar for data export should be create manually first.
