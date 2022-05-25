# hector_slam

This is a custom hector slam where the map update thresholds can be updated using rosparam commands in a terminal.

ROS Wiki: http://wiki.ros.org/hector_slam

-----
## Changes
### Fixing the map
When MallARD tilts on the water, it might see outside beyond the wall, which can cause its map being distorted and hence its localisation failing. To prevent this, you can fix the current map by stopping the map being updated. You can do this by increasing the angle and distance thresholds for the map to update. Refer to [README_El-MallARD](https://github.com/EEEManchester/MallARD/blob/main/README_El-MallARD.md) for the steps.

To enable this, the following commands have been added to [HectorMappingRos.cpp](hector_mapping/src/HectorMappingRos.cpp).
```
// a node handler
ros::NodeHandle map_update_nh("~");
// variables to keep rosparam values, initialised with the map update thresholds
double rosparam_map_update_angle_thresh = p_map_update_angle_threshold_;
double rosparam_map_update_distance_thresh = p_map_update_distance_threshold_;

...

/*
Get the values of the ROS parameters 'map_update_angle_thresh'
and 'map_update_distance_thresh' set by 'rosparam' commands in a terminal
*/
rosparam_map_update_angle_thresh = map_update_nh.param("map_update_angle_thresh", p_map_update_angle_threshold_);
rosparam_map_update_distance_thresh = map_update_nh.param("map_update_distance_thresh", p_map_update_distance_threshold_);

// Update the map update thresholds if there is difference
if (p_map_update_angle_threshold_ != rosparam_map_update_angle_thresh){
    p_map_update_angle_threshold_ = rosparam_map_update_angle_thresh;
    slamProcessor->setMapUpdateMinAngleDiff(p_map_update_angle_threshold_);
}
if (p_map_update_distance_threshold_ != rosparam_map_update_distance_thresh){
    p_map_update_distance_threshold_ = rosparam_map_update_distance_thresh;
    slamProcessor->setMapUpdateMinDistDiff(p_map_update_distance_threshold_);
}
```

Now you can change the map update thresholds by setting the corresponding ROS paramters 'map_update_angle_thresh' and 'map_update_distance_thresh' using 'rosparam' commands in a terminal. For example, you can set high thresholds to essentailly stop updating the map, in other words, fix the map.

```
rosparam set /hector_mapping/map_update_distance_thresh 1000    # in meters
rosparam set /hector_mapping/map_update_angle_thresh 1000       # in radians
```

### Improvement

Without fixing the map:
- The map can get permanently corrupted.
- Once the map gets corrupted, the robot might not be able to perform localisation correctly.

With fixing the map:
- The correct map initially created can stay uncorrupted.
- The robot can perform localisation correctly on the fixed map.
- Even when the robot goes outside the fixed map and therefore failes to localise itself, once it comes back inside the fixed map, the robot can perform localisation correctly on the fixed map with manually setting 2D pose estimate in RViz.
