# CarND-Path-Planning-Project

### 1. Generating Paths

The implementation uses the spline library (http://kluge.in-chemnitz.de/opensource/spline/) to generate trajectories and uses previous path points to make a smooth transition between cycles. The implementation is described in detail below:

- **Anchor Points For the Spline**: Five anchor points are used to fit a spline using which the waypoints are generated for the car to follow. The first two anchor points serve the purpose of smoothing the connection between cycles. So, these first two points are the last and the second last points from the previous path points received from the simulator. If the previous path list has less than two points, than the ego car's current position and yaw are used to create these points. Yaw is used so that we generate the point tangent to car's current heading. The remaining three anchor points are placed at 30m, 60m and 90m in the s coordinate ahead of the car. Their corresponding d is caculated based on the lane the car wishes to be in. These three pairs of (s,d) coordinates are translated to xy coordinates using the 'getXY' function.

- **Path Generation**: The five anchor points generated as described above are used to fit a spline. (x,y) points are generated along the spline and are spaced so as to not exceed the current reference velocity of the car. The implementation can be found between lines 401 and 428 in main.cpp. 

- **Smoothing**: In each cycle 50 waypoints are sent to the simulator for the car to follow. To make a smooth transition between cycles, instead of generating new waypoints every time, the previous path points (not traversed by the car) received from the simulator are used and only the deficit number of waypoints are generated.

### 2. Behavior Planning

The sensor fusion data received from the simulator is used to make decisions for the car to travel safely around the track avoiding collisions with other vehicles and making lane changes whenever necessary and safe. The sensor fusion data is processed to determine lane change safety and the correct velocity while keeping the current lane.The implementation is described in detail below:

**Process Sensor Fusion Data**: 
- The left lane is marked as safe if there are no vehicles within 30 meters in front and 30 meters behind the ego vehicle in the left lane.
- The right lane is marked as safe if there are no vehicles within 30 meters in front and 30 meters behind the ego vehicle in the right lane.
- If the ego vehicle is in the left most lane, than a left lane change is marked as unsafe.
- If the ego vehicle is in the right most lane, than a right lane change is marked as unsafe.
- If the gap between the ego vehicle and the vehicle in front is less than the set threshold (30 meters) than the state of the vehicle is marked as "too_close".

**Lane Change Decision**: 
- The vehicle keeps the current lane until it encounters a slow moving vehicle in front (identified by the "too_close" flag set while processing the sensor fusion data). 
- At this point the vehicle makes a lane change if it is safe to do so. Otherwise it reduces its speed and follows the vehicle in front until it is safe to make a lane change.

**Speed Control**: 
- When the car approaches a slow moving vehicle in front and lane changes are not safe, than the velocity is reduced at a constant rate of 5 meters per second until there is a safe gap. 
- When it is safe to do so the velocity of the vehicle is incrased at a constant rate of 5 meters per second. 

The source code pertaining to the behavior planner implementation can be found between lines 248 and 321 in main.cpp.

### 3. Result

The following screenshot shows the ego vehicle completing one lap without incident.

![Complete Lap](./images/complete_lap.png)
