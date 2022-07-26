Pixhawk Flight Records Visualization
====================================

PX4 has some online tools to visualize Pixhawk flight records we can use
to visualize previous flights on a map. However, we want to compare our
Python simulation results with the real results obtained from Pixhawk
records. Therefore, we developed a visualization tool in Python to draw
the traveled path of drones and animate their flights. To use this tool,
a user just has to put the ulog files of the drones he wants to
visualize in a folder and puts the directory of the folder as the
directory argument.

.. _1input-arguments:

1.Input arguments
-----------------

directory: directory of the folder containing all the ulog files of the
drones we want to visualize

ref_lat: latitude of geodetic origin reference point

ref_long: longitude of geodetic origin reference point

ref_alt: altitude of geodetic origin reference point

drone_size: size of drones in visualization

ticks_num: number of ticks for each Cartesian axis

Note: if a user does not provide one arguments of ref_lat, ref_long and
ref_alt, the function considers a point with the least latitude,
longitude and altitude as the reference point.

.. _2output-figure:

2.Output Figure
---------------

The output is an animated figure of all drones with interpolated
positions. Interpolation has been used because we do not have exactly
the same sample times from all the drones. The time sample of a drone
with the latest start time is considered to interpolate other drones'
positions. Note: Output figures are printed and rendered in your default
browser. To reach the maximum rate of animation and have better figures,
set Google Chrome as your default browser.
