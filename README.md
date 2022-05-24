# hector_slam

See the ROS Wiki for documentation: http://wiki.ros.org/hector_slam



Fixing map 

Custom Hector mapping

Using a node handler -> made it possible to get inputs using 'rosparam set' -> update 'p_map_update_distance_threshold_' and 'p_map_update_angle_threshold_' -> apply the values to 'map update' function  

Timing / TF issue - 'laser' to 'map' frames 

Issue: 

Separate scan for RViz 

Time delay (0.5s) applied 

Improvement? 

Without fixing the map: El can't essentially come back to the normal map? 

With fixing the map: El can come back, but with setting 2D pose estimate manually in RViz 