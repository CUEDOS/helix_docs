.. _1-centered-corridors:

1. Centered Corridors
=====================

We define centered corridors for our drones through our Ground Control
Station. The drones should decide how to move in these corridors without
collision in a decentralized manner. These centered corridors have two
specifications, their center points and directions. In what follows they
are described.

.. _11-points-of-corridors:

1.1. Points of Corridors
------------------------

We define a list of ordered points as center of a corridor in which each
drone should fly without collision based on their decentralized swarming
rules. It is noteworthy that the points are not time-based, but they are
ordered which means that when a drone is flying based on its current
point of the list, tries to pass the next point. When it has passed the
next point, the point is considered as the drone’s current one. The list
containing the points of each drone's corridor is sent to each drone
thorough our Ground Control Station. For instance, the equation of
center points of a corridor as an ellipse with origin point of (x0, y0,
z0) and width of 2a and length of 2b is: (x-x0)2/a2 +(y-y0)2/b2 = 1,
z=z0. The list containing coordination of the points is created through
the following piece of code in Python:

::

   Points=[]
   for i in range(points_number):
       Points.append(numpy.array([a*math.sin(i*6.28/points_number) + x_0, b*math.cos(i*6.28/points_number)+y_0, z_0]))

If we consider x0 as 25, y0 as 50, z0 as 20, a (width) as 25 and b
(length) as 50, we have an ellipse as shown below:

.. raw:: html

   <p align="center"><img src="https://github.com/CUEDOS/helixio/blob/main/img/Ellipse.png" width="3000")</p>

.. _12-directions-of-corridors:

1.2. Directions of Corridors
----------------------------

A direction vector at a point of a corridor, is a vector that a drone
should fly and move forward along with in the corridor. Therefore, we
create a list of directions, in which nth element (nth direction) is a
normalized vector from point nth to (n+1)th. The list containing vector
of directions is obtained through the following piece of code in Python:

::

   Directions=[]
   for i in range(len(Points)):
       if i==len(Points)-1: # for the last point
           Directions.append((Points[0]-Points[i])/numpy.linalg.norm(Points[0]-Points[i])) #normalized direction vector
       else:
           Directions.append((Points[i+1]-Points[i])/numpy.linalg.norm(Points[i+1]-Points[i])) #normalized direction vector

We use these direction vectors as the direction of the migration
velocity, and to obtain lane cohesion error vector described in what
follows.

.. _2-velocities--swarming-rules-during-a-mission:

2. Velocities & Swarming Rules During a Mission
===============================================

There are four target velocities to make drones move forward and rotate
at the right distance from the direction vectors without collision in
corridors. These velocities are described in what follows.

.. _21-lane-cohesion-velocity:

2.1. Lane Cohesion Velocity
---------------------------

We want each drone to rotate around the line from its current point to
the next point at a radius called lane radius. So we should have a
velocity/force to make the drone at the right distance (lane radius)
from the line. Therefore, we call this velocity/force as lane cohesion
velocity/force that provides centripetal force to keep the drone at the
right distance from the line. To obtain the magnitude and direction of
this velocity, at first, we define lane cohesion error as shown in the
following figure.

.. raw:: html

   <p align="center"><img src="https://github.com/CUEDOS/helixio/blob/main/img/Lane%20cohesion%20radius.png" width="600"></p>

As it seems from the previous figure, lane cohesion error is the
shortest vector from the line to the drone. So, magnitude of lane
cohesion velocity should be proportional to magnitude of lane cohesion
error minus lane radius, and its direction should be along with opposite
direction of the normalized lane cohesion error vector. Therefore, the
code to obtain it in Python is:

::

   P=Drone.position-Points[current_point]
   lane_cohesion_error=P-numpy.dot(P,Directions[current_point])*Directions[current_point]
   lane_cohesion_error_magnitude=np.linalg.norm(lane_cohesion_error)

   if np.linalg.norm(lane_cohesion_position_error) != 0:
       v_lane_cohesion = -1*(lane_cohesion_error_magnitude -lane_radius)*lane_cohesion_error/np.linalg.norm(lane_cohesion_error)

We will use lane cohesion error to obtain velocity of rotation in the
following sections.

.. _22-migration-velocity:

2.2. Migration Velocity
-----------------------

Migration velocity/force is along with the direction of the current
point (a normalized vector from the previous point of current point to
the current point) to make drones move forwad. Keep in mind that this
velocity is perpendicular to lane cohesion error and velocity. The
magnitude of this velocity is 1 that we can adjust it by gains assigned
by a user.

::

   v_migration = Directions[current_point]

.. _23-rotation-velocity:

2.3. Rotation Velocity
----------------------

Each drone should rotate around its current direction vector. The
direction of this rotation is perpendicular to both the direction vector
and lane cohesion error vector. So the direction of this velocity should
be the cross product of them. We define the magnitude of rotation
velocity as the magnitude of lane cohesion error over lane radius if
lane cohesion error is less than lane radius, and define it as lane
radius over the magnitude of lane cohesion error if lane cohesion error
is equal or greater than lane radius. Therefore, the magnitude is always
less than or equal to 1, and it decreases as a drone gets farther from
the lane radius around the direction vector of its current point.

::

   if lane_cohesion_error_magnitude < lane_radius:
       v_rotation_magnitude = (lane_cohesion_error_magnitude /lane_radius)
   else:
       v_rotation_magnitude = (lane_radius / lane_cohesion_error_magnitude)
   cross_prod=numpy.cross(lane_cohesion_error, directions[current_point]
   if (np.linalg.norm(cross_prod)) != 0):
       v_rotation = v_rotation_magnitude*cross_prod/numpy.linalg.norm(cross_prod))
   else:
       v_rotation = numpy.array([0, 0, 0])

.. _24-separation-velocity:

2.4. Separation Velocity
------------------------

To prevent collision of drones in swarming, we define a velocity/force
called separation velocity to separate them. Each drone has a feedback
of other drones' velocities, so each drone can calculate its own
separation velocity. The whole separation velocity for a drone, is the
sum of separation velocities between the drone and other drones. The
direction of each separation velocity is along with a vector from
another drone to the drone (to push the drone away), and its magnitude
is based on their distance. The figure of the magnitude of separation
velocity between two drones are shown below:

.. raw:: html

   <p align="center"><img src="https://github.com/CUEDOS/helixio/blob/images/img/V_separation.png" width="400"></p>

And the equation of separation velocity between two drone based on the
above figure is:

.. raw:: html

   <p align="center"><img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B80%7D%20%5Cbg_white%20%5Chuge%20%5Cleft%5C%7B%5Cbegin%7Bmatrix%7D%20%5C%21%20%5C%21%20%5C%21%20v_%7Bsep%7D%3D0%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%5C%2C%20%28d%3Er_%7Bconf%7D%29%20%5C%5C%20v_%7Bsep%7D%3D%5Cfrac%7B%5C%21%20%5C%21%20%5C%21%20%5C%21%20%5C%21%20%5C%21%20%5C%21%20%5C%21%20r_%7Bconf%7D%5C%2C%20-%5C%2C%20d%7D%7Br_%7Bconf%7D%5C%2C%20-%5C%2C%20r_%7Bcoll%7D%7D%20%5C%3A%20%5Cfrac%7B%5Cvec%7Bx%7D%7D%7Bd%7D%20%5C%3A%20%5C%3A%20%5C%3A%20%5C%3A%20%28r_%7Bcoll%7D%3C%20d%5Cleq%20r_%7Bconf%7D%29%20%5C%5C%20%5C%21%20%5C%21%20%5C%21%20v_%7Bsep%7D%3D1%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%5C%3B%20%28d%5Cleq%20r_%7Bcoll%7D%29%20%5Cend%7Bmatrix%7D%5Cright."></p>

In the above equation, rconf, rcoll and vsep refer to radius of conflict
area, radius of collision area and separation velocity, respectively.
The Python code to calculate total separation velocity is:

::

   v_separation = np.array([0, 0, 0])
   for drone in swarm:
       if drone == Drone:
           continue
       x = Drone.position - numpy.array(drone.position)
       d = np.linalg.norm(x)
       if d <= r_conflict and d > r_collision and d != 0:
           v_separation = v_separation + ((x / d) * (r_conflict - d / r_conflict - r_collision))
       if d <= r_collision and d != 0:
           v_separation = v_separation + 1 * (x / d)

Note that object Drone in the above code refers to the drone currently
running the code, and swarm contains objects of all the drones.

.. _25-passing-points:

2.5. Passing Points
-------------------

We should define a mechanism to check if the drone has passed the points
next to its current point or not. If we just consider closest distance
to determine the drone's current point, we will face a problem as shown
in the photo below:

.. raw:: html

   <p align="center">
   <img src="https://github.com/CUEDOS/helixio/blob/main/img/not%20passed%20points.png" width="900" height="whatever">
   </p>

As we can see, when the drone was in the middle of its way from point 0
to 1, it changed its current point to 1, and changed its path and
direction to point 1 to 2 without completing its path from point 0 to 1.
This problem is more sensible when the number of the points are not
high. To solve this problem and make the movement of the drones
smoother, we define a mechanism based on dot product (not closest
distance) to determine and find out when a drone has passed a point next
to its current point. The mechanism explains that a drone has passed
point m if:

1. It has passed all points from the current point to m-1.
2. The dot product of the vector from point m to drone and direction
   vector from point m-1 to m is equal or greater than zero. The dot
   product is shown as follows:

.. raw:: html

   <p align="center"><img src="https://github.com/CUEDOS/helixio/blob/main/img/Pass%20Plane.png" width="650"></p>
   Then if the drone has passed point m, point m will be the current point. We continue to find the passed points as long as the dot product for points after the current point is zero or positive. When the dot product is negative, the loop to find the current point stops until the next time we run the loop to find the current point.
   And the result of the mechanism for the previous example is:
   <p align="center"><img src="https://github.com/CUEDOS/helixio/blob/main/img/passed%20points.png" , width="900")</p>

.. _3-preparations-after-and-before-a-mission:

3. Preparations after and before a Mission
==========================================
