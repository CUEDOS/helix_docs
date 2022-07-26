Flocking algorithms can be tested in SITL by simulating multiple drones
which are each controlled by an instance of the same flocking script.
The PX4 Firmware includes a shell script for spawning multiple drones in
Gazebo, however each one requires an instance of the Mavsdk server and
flocking script. This document outlines how to set up a multi drone
simulation and run a basic flocking algorithm.

Spawning Multiple Drones in Gazebo
----------------------------------

1. Navigate to the directory where the PX4 Firmware is stored in
   terminal.
2. Navigate into the Firmware/Tools directory, inside is a script called
   "gazebo_sitl_multiple_run.sh".
3. Run the script using the -n flag to specify the number of drones to
   spawn, for example to spawn 6 drones:
   .. code:: sh

      bash gazebo_sitl_multiple_run.sh -n 6

This will spawn the speficied number of drones in the Gazebo simulation.
Each will communicate on UDP port 14550 meaning that they will all be
visible in QGroundcontrol, the first drone will also communicate on UDP
port 14540 with each following drone communicating on the next port in
sequence, 14541, 14542 etc.

Connecting to Multiple Drones with Mavsdk
-----------------------------------------
